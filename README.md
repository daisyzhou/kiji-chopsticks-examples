Examples for Chopsticks
========================

Chopsticks allows you to write [Scalding](https://github.com/twitter/scalding) that reads
and writes Kiji tables.

This project contains an example that counts the words in the 20 Newsgroups data set.

The following instructions assume that a functional
[KijiBento](https://github.com/kijiproject/kiji-bento/) minicluster has been
setup and is running, and that you have set up and installed
[Chopsticks](https://github.com/kijiproject/kiji-chopsticks) with the chop tool on your classpath.
This example uses the
[20Newsgroups](http://qwone.com/~jason/20Newsgroups/) dataset; you should download it
to load into a kiji table as outlined below.

If you haven't installed the default kiji instance yet, do so first:

    kiji install

Load the data
-------------

Next, create and populate the 'words' table:

    kiji-schema-shell --file=src/main/layout/words.ddl
    chop jar lib/kiji-chopsticks-examples-0.1.0-SNAPSHOT.jar \
        org.kiji.chopsticks.examples.NewsgroupLoader \
        kiji://.env/default/words <path/to/newsgroups/root/>

Read from a Kiji table
-------------------------

Run the word count, outputting to hdfs:

    chop hdfs lib/kiji-chopsticks-examples-0.1.0-SNAPSHOT.jar \
        org.kiji.chopsticks.examples.NewsgroupWordCount \
        --input kiji://.env/default/words --output ./wordcounts.tsv

Check the results of the job:

    hadoop fs -cat ./wordcounts.tsv/part-00000 | grep "\<foo\>"

You should see something similar to:

    "'foo'\''bar'". 1
    "foo"); 1
    "foo'bar",  1
    "foo.txt  1
    "foo.txt" 1
    "foo:0",  1
    <foo> 1
    <foo@cs.rice.edu> 1
    >foo  1
    `foo' 1
    bar!foo!frotz 1
    foo 2
    foo%bar.bitnet@mitvma.mit.edu 1
    foo-boo 1
    foo/file  1
    foo:  1
    foo@mhfoo.pc.my 1

Write to a Kiji table
-----------------------

This project also contains a trivial example of writing to a Kiji table.
NewsgroupWordDoubler reads from a kiji table and, for each word, writes
a doubled version of the word to the "info:doubleword" column.

To run the doubler, run:

    chop hdfs lib/kiji-chopsticks-examples-0.1.0-SNAPSHOT.jar \
        org.kiji.chopsticks.examples.NewsgroupWordDoubler \
        --input kiji://.env/default/words --output kiji://.env/default/words

Check the output in kiji:

    kiji scan kiji://.env/default/words --max-rows=10
