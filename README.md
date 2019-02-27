# Compress / Decompress functions based on Shox96 for SQLite3

This Sqlite3 [loadable extension](https://www.sqlite.org/loadext.html) exposes two functions `shox96_0_2c()` and `shox96_0_2d()` to any Sqlite3 database for compressing and decompressing text data.

# Preface

Shox96 is a compression technique developed for reducing storage size of Short Strings.  Details of how it works can be found [here](https://github.com/siara-cc/Shox96).

As of now it can work on only strings made of 'A to Z', 'a to z', '0-9', Special Characters such as &*() etc. found on keyboard, CR, LF, TAB and Space.

In general it can achieve upto 40% size reduction for Short Strings.

# This project

This project exposes the compression and decompression functions of Shox96 in the form of Sqlite Loadable extension.

The suffix of `0_2` indicates the version of the compression technique.  In case the compression algorithm is changed in future, this implementation will not be affected.  That means any text compressed with `shox96_0_2c()` can be uncompressed only by `shox96_0_2d()`.

# Compiling

To compile the loadable extension, use the following commands on `shox96_0_2.c` downloaded from this repository:

Unix / Linux: `gcc -g -fPIC -shared shox96_0_2.c -o shox96_0_2.so`

MacOS: `gcc -g -fPIC -dynamiclib shox96_0_2.c -o shox96_0_2.dylib`

Windows MSVC: `cl shox96_0_2.c -link -dll -out:shox96_0_2.dll`

Windows MinGW: gcc -g -shared shox96_0_2.c -o shox96_0_2.dll`

# Usage

After successful compilation, run `sqlite3` from the same folder to open the Sqlite3 console.  The following set of commands demonstrate how this extension can be used:

```sql
.open test.db
.load shox96_0_2
create table test (b1 blob);
insert into test values (shox96_0_2c('Hello World'));
insert into test values (shox96_0_2c('Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry''s standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book.'));
insert into test values (shox96_0_2c('Hello World Hello World Hello World Hello World '));
insert into test values (shox96_0_2c('This line is tooooooooooooooooooooooooooooooooooooooooooooooooooo long'));
select txt, length(txt) txt_len, clen from (select shox96_0_2d(b1) txt, length(b1) clen from test);
```

# Screenshots

![](output.png?raw=true)

# Limitations

- Trying to decompress any blob that was not compressed using `shox96_0_2c()` will crash the program.
- This works well with short strings as shown above.  For longer strings, use [compress](https://www.sqlite.org/src/file/ext/misc/compress.c) loadable extension provided in Sqlite website.

# Issues

Please contact the author if you find any problem or create issue here.
