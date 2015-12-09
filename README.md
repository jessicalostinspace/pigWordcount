# pigWordcount
A tutorial for a simple wordcount assignment in PIG
This tutorial will use PIG in mapreduce via the terminal statement:
pig -x mapreduce

1. Create two files on your local drive with the following text:
    A long time ago in a galaxy far far away  ----> put this text in a file called testfile1
    Another episode of Star Wars  ----> put this text in a file called testfile2

    On my system, these files are stored in:
    /home/cloudera

2. Since we're using mapreduce, we need to set up directories in HDFS so we can move the files from local to HDFS:
   hdfs dfs -mkdir /user/cloudera/pigin
   hdfs dfs -copyFromLocal /home/cloudera/testfile* /user/cloudera/pigin
  
   We also need an output folder in HDFS:
   hdfs dfs -mkdir /user/cloudera/pigoutnew
 
   Also, don't forget that when you rerun scripts you might have to remove the output files or output directory otherwise you    get an error saying file already exists.

3. Create the following PIG file (wordcount.pig) or copy into interactive grunt terminal:
   
   - If running in interactive terminal, start grunt by typing into terminal:
   pig -x mapreduce
   - If running directly from pig file:
   pig -x mapreduce
   exec wordcount.pig

   -- Load the testfiles into PIG storage:
   wordfile = LOAD '/user/cloudera/pigin/testfile*' USING PigStorage('\n') as (linesin:chararray);
   wordfile_flat = FOREACH wordfile GENERATE FLATTEN(TOKENIZE(linesin)) as wordin;
   wordfile_grpd = GROUP wordfile_flat by wordin;
   DESCRIBE wordfile_grpd;
   word_counts = FOREACH wordfile_grpd GENERATE group, COUNT(wordfile_flat.wordin);
   STORE word_counts into '/user/cloudera/pigoutnew/word_counts_pig';

4. Move HDFS output files to local system:
   hdfs dfs -copyToLocal /user/cloudera/pigoutnew/word_counts_pig /home/cloudera/word_counts_pig

5. You can dump the contents to see what you have:
   DUMP wordfile_grpd;

   
