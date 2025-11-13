# Assignment 4 - Columnar Databases and HBase

## 7in7 - HBase - Day 1

### Part 1 - Interactive Reading

Read Day 1 and work through the examples in the chapter.
Save your final database in a directory `day1` as follows.

```
./save.bash wiki day1
```

You can use the same script to save your database between work sessions.
But you cannot save over an existing save. So use temporary names like:
`day1a`, `day1b`, etc. You can use the `./load.bash` script to restore
an empty database from a saved state.

Here are some additional tips:

* Skip **Configuring HBase**.
* Begin with the last command on p57: `version`.
* p59 - Do not run these in the shell, which is Ruby.
    These are illustrative examples in python.
* p64 - The last example is split over 2 lines. `hbase>` and `hbase*` are
    not part of the command.
* I have not been able to use the colons inside of quotes in the hbase commands. If they don't work with the colon, try them without.
* The command to allow unlimited  versions is:

`alter 'wiki', {NAME => 'text', VERSIONS => 2147483647 }`

### Part 2 - 7in7 - HBase Day 1 - Find

1. Figure our how to use the shell to do the following:

    * Delete individual column values in a row:

        ```
        hbase(main):012:0> delete 'wiki', 'Home', 'text:'
0 row(s) in 0.0260 seconds

hbase(main):013:0> get 'wiki', 'Home'
COLUMN                              CELL
0 row(s) in 0.0130 seconds
        ```

    * Delete an entire row

        ```
        hbase(main):014:0> deleteall 'wiki', 'Home'
0 row(s) in 0.0100 seconds

hbase(main):015:0> get 'wiki', 'Home'
COLUMN                              CELL
0 row(s) in 0.0080 seconds
        ```


2. Bookmark the HBase API documentation for the version of HBase you’re using.

    ```
    https://hbase.apache.org/2.4/apidocs/
    ```

### Part 3 - Create a family database

#### Step 1 - Create an hbase table that represents a family.

Specifically, you should have column families for the following:
* personal information: names of family members and birthdays
* favorites: foods and vacation locations
* location information: addresses including street, city, state, and zip. and phone numbers

Place the Hbase code to create the families below:
```
hbase(main):001:0> create 'family', { NAME => 'personal information' }, { NAME => 'favorites' }, { NAME => 'location information' }
0 row(s) in 2.4100 seconds

=> Hbase::Table - family
hbase(main):002:0> describe 'family'
Table family is ENABLED
family
COLUMN FAMILIES DESCRIPTION
{NAME => 'favorites', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING =>
 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE =>
'0'}
{NAME => 'location information', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_
ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => '65536', REPLICATIO
N_SCOPE => '0'}
{NAME => 'personal information', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_
ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => '65536', REPLICATIO
N_SCOPE => '0'}
3 row(s) in 0.0830 seconds


```

#### Step 2 - Load five rows of data.
Make sure to have at least one row with more than one favorite food and at least one row with more than one favorite vacation location. Place the Hbase code below:
```
hbase(main):003:0> put 'family', 'mom', 'personal information:name', 'Vasundara'
0 row(s) in 0.0810 seconds

hbase(main):004:0> put 'family', 'mom', 'favorites:food1', 'Ragi Sangati'
0 row(s) in 0.0090 seconds

hbase(main):005:0> put 'family', 'mom', 'favorites:vacation1', 'Kadapa'
0 row(s) in 0.0120 seconds

hbase(main):006:0> put 'family', 'mom', 'favorites:vacation2', 'Kurnool'
0 row(s) in 0.0110 seconds

hbase(main):007:0> put 'family', 'mom', 'location information:city', 'Anantapur'
0 row(s) in 0.0080 seconds
hbase(main):010:0> put 'family', 'mom', 'location information:city', 'Chitoor'
0 row(s) in 0.0270 seconds

hbase(main):011:0> scan 'family'
ROW                                 COLUMN+CELL
 mom                                column=favorites:food1, timestamp=1763007816659, value=Ragi Sangati
 mom                                column=favorites:vacation1, timestamp=1763007857044, value=Kadapa
 mom                                column=favorites:vacation2, timestamp=1763007886125, value=Kurnool
 mom                                column=location information:city, timestamp=1763008091311, value=Chitoor
 mom                                column=personal information:name, timestamp=1763007774677, value=Vasundara
1 row(s) in 0.0270 seconds

hbase(main):012:0> put 'family','grandma','personal information:name','Amma'
0 row(s) in 0.0260 seconds

hbase(main):013:0> put 'family','grandma','favorites:food1','Natu Kodi'
0 row(s) in 0.0090 seconds

hbase(main):014:0> put 'family','grandma','favorites:vacation1','Hyderabad'
0 row(s) in 0.0100 seconds

hbase(main):015:0> put 'family','grandma','location information:city','Lingala'
0 row(s) in 0.0090 seconds

hbase(main):016:0> scan 'family'
ROW                                 COLUMN+CELL
 grandma                            column=favorites:food1, timestamp=1763008384195, value=Natu Kodi
 grandma                            column=favorites:vacation1, timestamp=1763008415391, value=Hyderabad
 grandma                            column=location information:city, timestamp=1763008466606, value=Lingala
 grandma                            column=personal information:name, timestamp=1763008352862, value=Amma
 mom                                column=favorites:food1, timestamp=1763007816659, value=Ragi Sangati
 mom                                column=favorites:vacation1, timestamp=1763007857044, value=Kadapa
 mom                                column=favorites:vacation2, timestamp=1763007886125, value=Kurnool
 mom                                column=location information:city, timestamp=1763008091311, value=Chitoor
 mom                                column=personal information:name, timestamp=1763007774677, value=Vasundara
2 row(s) in 0.0250 seconds

hbase(main):017:0> get 'family', 'grandma'
COLUMN                              CELL
 favorites:food1                    timestamp=1763008384195, value=Natu Kodi
 favorites:vacation1                timestamp=1763008415391, value=Hyderabad
 location information:city          timestamp=1763008466606, value=Lingala
 personal information:name          timestamp=1763008352862, value=Amma
4 row(s) in 0.0270 seconds

hbase(main):019:0> put 'family', 'dad', 'personal information:name', 'Raghu'
0 row(s) in 0.0090 seconds

hbase(main):020:0> put 'family', 'dad', 'favorites:food1', 'Mutton Ghee Roast'
0 row(s) in 0.0110 seconds

hbase(main):021:0> put 'family', 'dad', 'favorites:food2', 'Thala Kura'
0 row(s) in 0.0050 seconds

hbase(main):022:0> put 'family', 'dad', 'favorites:vacation1', 'Kadapa'
0 row(s) in 0.0100 seconds

hbase(main):023:0> put 'family', 'dad', 'location information:city','Kurnool'
0 row(s) in 0.0100 seconds

hbase(main):024:0> scan 'family'
ROW                                 COLUMN+CELL
 dad                                column=favorites:food1, timestamp=1763008978949, value=Mutton Ghee Roast
 dad                                column=favorites:food2, timestamp=1763008998402, value=Thala Kura
 dad                                column=favorites:vacation1, timestamp=1763009021269, value=Kadapa
 dad                                column=location information:city, timestamp=1763009039987, value=Kurnool
 dad                                column=personal information:name, timestamp=1763008944744, value=Raghu
 grandma                            column=favorites:food1, timestamp=1763008384195, value=Natu Kodi
 grandma                            column=favorites:vacation1, timestamp=1763008415391, value=Hyderabad
 grandma                            column=location information:city, timestamp=1763008466606, value=Lingala
 grandma                            column=personal information:name, timestamp=1763008352862, value=Amma
 mom                                column=favorites:food1, timestamp=1763007816659, value=Ragi Sangati
 mom                                column=favorites:vacation1, timestamp=1763007857044, value=Kadapa
 mom                                column=favorites:vacation2, timestamp=1763007886125, value=Kurnool
 mom                                column=location information:city, timestamp=1763008091311, value=Chitoor
 mom                                column=personal information:name, timestamp=1763007774677, value=Vasundara
3 row(s) in 0.0440 seconds

hbase(main):025:0> get 'family', 'dad'
COLUMN                              CELL
 favorites:food1                    timestamp=1763008978949, value=Mutton Ghee Roast
 favorites:food2                    timestamp=1763008998402, value=Thala Kura
 favorites:vacation1                timestamp=1763009021269, value=Kadapa
 location information:city          timestamp=1763009039987, value=Kurnool
 personal information:name          timestamp=1763008944744, value=Raghu
5 row(s) in 0.0090 seconds

hbase(main):026:0> put 'family', 'child2', 'personal information:name', 'Keerthi'
0 row(s) in 0.0100 seconds

hbase(main):027:0> put 'family','child2','favorites:food1','Biryani'
0 row(s) in 0.0110 seconds

hbase(main):028:0> put 'family','child2','favorites:vacation1','Lingala'
0 row(s) in 0.0080 seconds

hbase(main):029:0> put 'family','child2','location information:city','Sanathnagar'
0 row(s) in 0.0090 seconds

hbase(main):030:0> put 'family','child2','favorites:food2','Kheema'
0 row(s) in 0.0120 seconds

hbase(main):031:0> scan 'family'
ROW                                 COLUMN+CELL
 child2                             column=favorites:food1, timestamp=1763009387603, value=Biryani
 child2                             column=favorites:food2, timestamp=1763009518024, value=Kheema
 child2                             column=favorites:vacation1, timestamp=1763009416303, value=Lingala
 child2                             column=location information:city, timestamp=1763009480332, value=Sanathnagar
 child2                             column=personal information:name, timestamp=1763009315133, value=Keerthi
 dad                                column=favorites:food1, timestamp=1763008978949, value=Mutton Ghee Roast
 dad                                column=favorites:food2, timestamp=1763008998402, value=Thala Kura
 dad                                column=favorites:vacation1, timestamp=1763009021269, value=Kadapa
 dad                                column=location information:city, timestamp=1763009039987, value=Kurnool
 dad                                column=personal information:name, timestamp=1763008944744, value=Raghu
 grandma                            column=favorites:food1, timestamp=1763008384195, value=Natu Kodi
 grandma                            column=favorites:vacation1, timestamp=1763008415391, value=Hyderabad
 grandma                            column=location information:city, timestamp=1763008466606, value=Lingala
 grandma                            column=personal information:name, timestamp=1763008352862, value=Amma
 mom                                column=favorites:food1, timestamp=1763007816659, value=Ragi Sangati
 mom                                column=favorites:vacation1, timestamp=1763007857044, value=Kadapa
 mom                                column=favorites:vacation2, timestamp=1763007886125, value=Kurnool
 mom                                column=location information:city, timestamp=1763008091311, value=Chitoor
 mom                                column=personal information:name, timestamp=1763007774677, value=Vasundara
4 row(s) in 0.0670 seconds

hbase(main):032:0> get 'family', 'child2'
COLUMN                              CELL
 favorites:food1                    timestamp=1763009387603, value=Biryani
 favorites:food2                    timestamp=1763009518024, value=Kheema
 favorites:vacation1                timestamp=1763009416303, value=Lingala
 location information:city          timestamp=1763009480332, value=Sanathnagar
 personal information:name          timestamp=1763009315133, value=Keerthi
5 row(s) in 0.0120 seconds

hbase(main):033:0> put 'family', 'dog', 'personal information:name', 'Pepsi'
0 row(s) in 0.0080 seconds

hbase(main):034:0> put 'family', 'dog', 'favorites:food1', 'Drools'
0 row(s) in 0.0120 seconds

hbase(main):035:0> put 'family', 'dog', 'favorites:food2', 'Raw Meat'
0 row(s) in 0.0090 seconds

hbase(main):036:0> put 'family', 'dog', 'favorites:vacation1', 'Our Farm'
0 row(s) in 0.0070 seconds

hbase(main):037:0> put 'family', 'dog', 'location information:city', 'Lingala'
0 row(s) in 0.0070 seconds

hbase(main):038:0> scan 'family'
ROW                                 COLUMN+CELL
 child2                             column=favorites:food1, timestamp=1763009387603, value=Biryani
 child2                             column=favorites:food2, timestamp=1763009518024, value=Kheema
 child2                             column=favorites:vacation1, timestamp=1763009416303, value=Lingala
 child2                             column=location information:city, timestamp=1763009480332, value=Sanathnagar
 child2                             column=personal information:name, timestamp=1763009315133, value=Keerthi
 dad                                column=favorites:food1, timestamp=1763008978949, value=Mutton Ghee Roast
 dad                                column=favorites:food2, timestamp=1763008998402, value=Thala Kura
 dad                                column=favorites:vacation1, timestamp=1763009021269, value=Kadapa
 dad                                column=location information:city, timestamp=1763009039987, value=Kurnool
 dad                                column=personal information:name, timestamp=1763008944744, value=Raghu
 dog                                column=favorites:food1, timestamp=1763009885085, value=Drools
 dog                                column=favorites:food2, timestamp=1763009920194, value=Raw Meat
 dog                                column=favorites:vacation1, timestamp=1763009956398, value=Our Farm
 dog                                column=location information:city, timestamp=1763009993867, value=Lingala
 dog                                column=personal information:name, timestamp=1763009841560, value=Pepsi
 grandma                            column=favorites:food1, timestamp=1763008384195, value=Natu Kodi
 grandma                            column=favorites:vacation1, timestamp=1763008415391, value=Hyderabad
 grandma                            column=location information:city, timestamp=1763008466606, value=Lingala
 grandma                            column=personal information:name, timestamp=1763008352862, value=Amma
 mom                                column=favorites:food1, timestamp=1763007816659, value=Ragi Sangati
 mom                                column=favorites:vacation1, timestamp=1763007857044, value=Kadapa
 mom                                column=favorites:vacation2, timestamp=1763007886125, value=Kurnool
 mom                                column=location information:city, timestamp=1763008091311, value=Chitoor
 mom                                column=personal information:name, timestamp=1763007774677, value=Vasundara
5 row(s) in 0.0340 seconds

hbase(main):039:0> get 'family', 'dog'
COLUMN                              CELL
 favorites:food1                    timestamp=1763009885085, value=Drools
 favorites:food2                    timestamp=1763009920194, value=Raw Meat
 favorites:vacation1                timestamp=1763009956398, value=Our Farm
 location information:city          timestamp=1763009993867, value=Lingala
 personal information:name          timestamp=1763009841560, value=Pepsi
5 row(s) in 0.0060 seconds


```

#### Step 3 – Create HBase queries for the items below.

Place the Hbase code **and the results** after each query.

**Query 1:** Get complete information for a specific family member.
```
hbase(main):040:0> get 'family', 'dog'
COLUMN                              CELL
 favorites:food1                    timestamp=1763009885085, value=Drools
 favorites:food2                    timestamp=1763009920194, value=Raw Meat
 favorites:vacation1                timestamp=1763009956398, value=Our Farm
 location information:city          timestamp=1763009993867, value=Lingala
 personal information:name          timestamp=1763009841560, value=Pepsi
5 row(s) in 0.0120 seconds
```

**Query 2:** View only personal information for all family members.
```
hbase(main):041:0> scan 'family', { COLUMNS => 'personal information' }
ROW                                 COLUMN+CELL
 child2                             column=personal information:name, timestamp=1763009315133, value=Keerthi
 dad                                column=personal information:name, timestamp=1763008944744, value=Raghu
 dog                                column=personal information:name, timestamp=1763009841560, value=Pepsi
 grandma                            column=personal information:name, timestamp=1763008352862, value=Amma
 mom                                column=personal information:name, timestamp=1763007774677, value=Vasundara
5 row(s) in 0.0120 seconds
```

**Query 3:** Get the name, favorite foods, and vacation locations for one family member.
```
hbase(main):042:0> get 'family', 'mom', { COLUMNS => ['personal information:name', 'favorites'] }
COLUMN                              CELL
 favorites:food1                    timestamp=1763007816659, value=Ragi Sangati
 favorites:vacation1                timestamp=1763007857044, value=Kadapa
 favorites:vacation2                timestamp=1763007886125, value=Kurnool
 personal information:name          timestamp=1763007774677, value=Vasundara
4 row(s) in 0.0070 seconds
```

**Query 4:** Get a range of at least two family members.
```
hbase(main):043:0> scan 'family', { STARTROW => 'child2', STOPROW => 'dad~' }
ROW                                 COLUMN+CELL
 child2                             column=favorites:food1, timestamp=1763009387603, value=Biryani
 child2                             column=favorites:food2, timestamp=1763009518024, value=Kheema
 child2                             column=favorites:vacation1, timestamp=1763009416303, value=Lingala
 child2                             column=location information:city, timestamp=1763009480332, value=Sanathnagar
 child2                             column=personal information:name, timestamp=1763009315133, value=Keerthi
 dad                                column=favorites:food1, timestamp=1763008978949, value=Mutton Ghee Roast
 dad                                column=favorites:food2, timestamp=1763008998402, value=Thala Kura
 dad                                column=favorites:vacation1, timestamp=1763009021269, value=Kadapa
 dad                                column=location information:city, timestamp=1763009039987, value=Kurnool
 dad                                column=personal information:name, timestamp=1763008944744, value=Raghu
2 row(s) in 0.0220 seconds
```

**Query 5:** Get the addresses for all family members.
```
hbase(main):044:0> scan 'family', {COLUMNS => ['location information:street', 'location information:city', 'location information:state', 'location information:zip', 'location information:phone'] }
ROW                                 COLUMN+CELL
 child2                             column=location information:city, timestamp=1763009480332, value=Sanathnagar
 dad                                column=location information:city, timestamp=1763009039987, value=Kurnool
 dog                                column=location information:city, timestamp=1763009993867, value=Lingala
 grandma                            column=location information:city, timestamp=1763008466606, value=Lingala
 mom                                column=location information:city, timestamp=1763008091311, value=Chitoor
5 row(s) in 0.0140 seconds
```

**Query 6:** Get the names of family members who like a specific favorite food (e.g., pizza).
```
hbase(main):045:0> scan 'family', { COLUMNS => 'favorites', FILTER => "ValueFilter(=, 'substring:Ragi Sangati')" }
ROW                                 COLUMN+CELL
 mom                                column=favorites:food1, timestamp=1763007816659, value=Ragi Sangati
1 row(s) in 0.0390 seconds
```

**Query 7:** Create a vacation preference list with names.
```
hbase(main):046:0> scan 'family', { COLUMNS => ['personal information','favorites'], FILTER => "MultipleColumnPrefixFilter('name','vacation')" }
ROW                                 COLUMN+CELL
 child2                             column=favorites:vacation1, timestamp=1763009416303, value=Lingala
 child2                             column=personal information:name, timestamp=1763009315133, value=Keerthi
 dad                                column=favorites:vacation1, timestamp=1763009021269, value=Kadapa
 dad                                column=personal information:name, timestamp=1763008944744, value=Raghu
 dog                                column=favorites:vacation1, timestamp=1763009956398, value=Our Farm
 dog                                column=personal information:name, timestamp=1763009841560, value=Pepsi
 grandma                            column=favorites:vacation1, timestamp=1763008415391, value=Hyderabad
 grandma                            column=personal information:name, timestamp=1763008352862, value=Amma
 mom                                column=favorites:vacation1, timestamp=1763007857044, value=Kadapa
 mom                                column=favorites:vacation2, timestamp=1763007886125, value=Kurnool
 mom                                column=personal information:name, timestamp=1763007774677, value=Vasundara
5 row(s) in 0.0270 seconds
```

### Part 4 - 7in7 - Day 3 - Wrap Up

Read Day 3 - Wrap Up. Then answer the following.

1. List the pros of HBase as described in our text.

    ```
1. It can store very large tables and grows easily by adding more machines.
2. It gives fast reads and writes using row keys.
3. It does not require a fixed schema and allows flexible columns.
    ```

2. List the cons of HBase as described in our text.

    ```
    1. It does not support joins or complex SQL queries.
    2. Data modeling is harder and depends heavily on good row-key design.
    3. Full table scans can be slow.
    ```


### 7in7 - Day 2 - OPTIONAL

OPTIONAL - You may safely skip Day 2.

This section contains a code-heavy example of loading a large amount
of data into your HBase database. If you feel like a challenge and are
interested, feel free to work through it.

If you do this section, please **do not** use ./save.bash to save
your database, because it may become very large.

So if you are ready for the challenge, below are some instructions to
help you along. Good luck!

1. Download and extract the source code for the text: https://pragprog.com/titles/pwrdata/seven-databases-in-seven-weeks-second-edition/

2. Drag the following files into 02_hbase/local/scripts on GitPod.
    * source_code/02_hbase/import_from_wikipedia.rb
    * source_code/02_hbase/create_wiki_schema.rb

3. Start your database.

4. Run the following to create the wiki table.

    ```
    ./shell.bash create_wiki_schema.rb
    ```

5. Now you should be able to run the command below.
    BEFORE YOU DO... be ready to press CTRL+C to stop the process. This
    command will load a lot of data very fast.

    ```
    curl https://dumps.wikimedia.org/enwiki/latest/enwiki-latest-pages-articles.xml.bz2 | bzcat | ./shell.bash import_from_wikipedia.rb
    ```

6. After it appears to be working, press CTRL+C to stop it.

7. Connect to your database.

8. Run the command below to count the number of
    rows in your 'wiki' table.

    ```
    count 'wiki'
    ```

    Copy and paste the output of this command below.

9. Run the command below to get information about your database's regions.

    ```
    scan 'hbase:meta',{FILTER=>"PrefixFilter('wiki')"}
    ```

    Copy and past the output of this command below.
