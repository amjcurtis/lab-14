# Database Migration

## Queries Run

`CREATE TABLE BOOKSHELVES (id SERIAL PRIMARY KEY, name VARCHAR(255));`
* Create second table called `bookshelves` in `books_app` DB

`INSERT INTO bookshelves(name) SELECT DISTINCT bookshelf FROM books;`
* Use simple subquery to retrieve unique bookshelf values from `books` table and insert each one into `bookshelves` table in the `name` column. (Great pattern for copying lots of data between tables.)

`ALTER TABLE books ADD COLUMN bookshelf_id INT;`
* Add column named `bookshelf_id` to `books` table. This connects each book to specific bookshelf in the other table.

`UPDATE books SET bookshelf_id=shelf.id FROM (SELECT * FROM bookshelves) AS shelf WHERE books.bookshelf = shelf.name;`
* Prepare a connection between the two tables. Works by running subquery for every row in the `books` table. Subquery finds bookshelf row that has `name` matching current book's `bookshelf` value. The `id` of that bookshelf row is then set as value of the `bookshelf_id` property in current book row.

`ALTER TABLE books DROP COLUMN bookshelf;`
* Modify `books` table by removing column named `bookshelf`. The `books` table now contains a `bookshelf_id` column that becomes foreign key, so table doesn't need to contain a string representing each bookshelf anymore.

`ALTER TABLE books ADD CONSTRAINT fk_bookshelves FOREIGN KEY (bookshelf_id) REFERENCES bookshelves(id);`
* Modify data type of the `bookshelf_id` in `books` table, setting it as foreign key that references primary key in `bookshelves` table. Now PostgreSQL knows _how_ the two tables are connected.