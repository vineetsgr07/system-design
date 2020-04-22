# Indices

Indices allow us to quickly access records in the database.
[B-trees] are typically used to implement indices.
There are two costs to using indices:

* Slower write times
* More space usage

Indices essentially allow us to locate data without having to visit every row of a database.

Indices can be thought of just like the "index" portion at the back of books.
You could index users by their eye color, and that would allow you to quickly find all users with green eyes.

When a database is terabytes in size with records being small in size, indices are a necessity for optimizing data access.
When data of this magnitude is spread across multiple database servers, it becomes even more necessary to ensure that each server is properly indexed.

[B-trees]: http://use-the-index-luke.com/sql/anatomy/the-tree
