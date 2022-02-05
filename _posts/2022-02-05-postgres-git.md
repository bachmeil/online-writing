---
layout: post
title: Can you put a Postgres database in a Git repo?
---
I am not a professional software developer, and even though I teach an introduction to SQL in one of my classes, I am not a database expert. More precisely, I don't know much about the server side of databases. I'm an intermediate-level writer of queries. Mostly using SQLite.

Databases are nice, but you need to keep them in sync, which makes them a pain for the data in your personal apps. Is there a way we could use, say, PostgreSQL (PG) and sync it using Git?

You might ask why not set up a PG server and use that. Well then you're in the business of setting up and securing a PG server. Not fun. Not how I want to spend my time or money. Supabase and others fix those issues, but then you're reliant on an always-available internet connection.

Git has solved our syncing, backup, and security issues for text documents. Can we use Git as a way to keep a PG database in sync across all of our machines? Could we use PG as the backend to store and query information in our apps?

**How can you make it work?** The first question is how to make the concept work at all. In other words, what would be the simple version of a PG database that a single user could use on multiple machines with Git syncing?

*Simple solution.* Suppose you only have a few hundred records in your database. You use your data on machine A. When you're done making arbitrary changes, you dump the database to a text file, commit, and push it to the server. A few days later you work on machine B in a different location. You pull the changes to the repo, read everything into a new database, make arbitrary changes, commit, and push to the server.

That's simple, but flawed. 

- What if you forget to pull before you start making changes to the database? You will potentially end up with all kinds of merge conflicts. 
- What if you forget to push on machine B, then make changes on machine A, and then pull into machine B? Although the Git repo provides some comfort by retaining the full history of the database, that's not too helpful in practice, due to the fact that the dump itself only retains the state of the database at the time of the dump. It does not include the full history. You're reading in the most recent data dump and building on that.

The simple strategy is viable only if you have some way to guarantee that the version of the database on each machine is synced with the server at the start and end of each session. I'm not aware of any way to do that. Dropbox could handle the syncing, but then you shift to the problem of needing to know when the database needs to be recreated. That's not a solution, and given that Dropbox is not a version control system, it would be even harder trying to fix problems when they occur. Git pushes and pulls provide a clean "notification" that it's time to recreate your database.

*Append-only text files.* We can avoid a messed-up database by using append-only text files and keeping the full history inside the database itself. On every change to the data, you track both the record's id and the modification time, and you append them to a text file. Rather than doing a data dump, which strips the history of the database, you store changes at the end of the text file. For each record id, you do an update on read if the modification time is later than the current modification time.

This doesn't fix everything:

- The database size would explode if you added many small changes to a large text document. If you recorded 100 small changes to a 100K document, that would be a total of 10 MB of additional space. It's not an issue at all as the size of the documents and the number of edits gets small. For a todo list, where you'd mainly be creating new tasks and then marking them as done, it wouldn't be relevant. If you wanted to store large, frequently changing text documents, you'd need another solution. Of course, that solution is Git. You only need to store the metadata for the file in the database. I'm having a hard time coming up with a case where the exploding database size would ever be an issue for personal data. If you have a hundred million items on your todo list, you should get some counseling.
- The other potential issue is that you could make edits to an old version of a record. If you forget to pull before editing, you might not be editing the latest version. The "syncing" strategy is "last edit wins". That's not too big an issue since the full history is in the database, not just Git.

Subject to the above limitations, you now have a working database sync using a Git repo.

*Avoiding merge conflicts.* The append-only text file approach would work, but it would introduce the possibility of merge conflicts. If you're building on an old version of the database, you're appending to an old version of the data file. Then you'll do a push and pull and have to figure it out. It's not that big of a problem, really, because you keep everything and the ordering of the lines is irrelevant. To my knowledge Git offers no way to keep all lines in both files with no concern about the order. (And that doesn't make sense for most programs.)

The solution to this is easy. Each machine gets its own data file. All appends are done to that file. After doing a pull, the database is created by reading in all of the individual text files in arbitrary order. This can easily accommodate several dozen machines without ever producing a conflict, which is impossible.

"Last version wins" resolution, append-only text files, and different files for each machine provides a complete solution for a small database. Can we make this scale to several GB of data? I think you can. After a certain amount of time passes, you'll have synced all of the machines. You can move the old data from the individual files into an archive file that is the foundation for all of the machines. You would not need to ever read those records in unless you added a new machine. It would be no problem to keep the most recent two months of data in individual text files and everything else in the archive file. If your app detected a change in the archive file, it would read the archive file in again. Even with tens of thousands of records in the individual data files, this would be a practical solution.

As long as you're the only one using this system, I don't see what would prevent it from doing *everything* you need to do in your apps.