# assignment-1
## I. Get the data files
Download the following data files from the internet using the `curl` command: `http://eaton-lab.org/pdsb/test.fastq.gz` and `http://eaton-lab.org/pdsb/iris-data-dirty.csv`. Use the `less` or `zless` commands to look at the files. Then use the `head` command to print the first 5 lines from each file as output.

According to the Lecture 1 powerpoint, I used the `curl` command followed by `-O` to download the file `test.fastq.gz` from the given website to the home directory. Then I used `zless` command followed by the file name to view the file. In order to print the first 5 lines, I first used the `gunzip` command to unzip the file. And then I used the `head` command followed by `-n5` and file name to print the first 5 lines (Referencing the website http://www.linfo.org/head.html). Similarly, I used the `curl` command followed by `-O` to download the file `iris-data-dirty.csv` from the given website to the home directory. Then I used `less` command to view the file. Finally I used the `head` command followed by `-n5` and file name to print the first 5 lines.

```
curl -O http://eaton-lab.org/pdsb/test.fastq.gz
zless test.fastq.gz
gunzip test.fastq.gz
head -n5 test.fastq

curl -O http://eaton-lab.org/pdsb/iris-data-dirty.csv
less iris-data-dirty.csv
head -n5 iris-data-dirty.csv
```

```
@29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
TGCAGGAAGGAGATTTTCGNACGTAGTGNNNNNNNNNNNNNNGCCNTGGATNNANNNGTGTGCGTGAAGAANAN
+29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
IIIIIIIGIIIIIIFFFFF#EEFE<?################################################
@29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74

5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
```

## II. Clean the data
Use `grep`, `uniq`, sed. Check that all of the species names are spelled correctly in the file `iris-data-dirty.csv`. Also check for missing values stored as `NA`. Create a new file where mispelled names are replaced with the correct values, and lines with `NA` are excluded, and save it as `iris-data-clean.csv`. Use `cut`, `sort`, and `uniq` to list the number of data values there are for each species in the new cleaned data file.

First use the `cp` command to copy the file `iris-data-dirty.csv` to a new file `iris-data-clean.csv`. Notice that there are three different species: sertosa, versicolor, and virginica. Use the `grep` inverse command to find the mispelled species, and correct them using the `sed` command to replace the mispelled species to correctly spelled species (Referencing the website https://stackoverflow.com/questions/3548453/negative-matching-using-grep-match-lines-that-do-not-contain-foo). Then use the `sed` command again to remove any line that contains the string `NA`. In order to number of data values for each species, I used the `sort` command with the `uniq` command to eliminate the repeated data. This is followed by the `cut` and another `uniq` command to cut the species names out and count how many times the species names are repeated, which indicates the number of data values of a certain species. Specifically, I used `-` as the delimiter to seperate the two fields and the species names belong to the second field (Referencing the website https://www.computerhope.com/unix/ucut.htm).

```
cp iris-data-dirty.csv iris-data-clean.csv
grep -v 'virginica\|setosa\|versicolor' iris-data-clean.csv
sed -i -e 's/setsa/setosa/g' iris-data-clean.csv
sed -i -e 's/versicolour/versicolor/g' iris-data-clean.csv
sed -i -e '/NA/d' iris-data-clean.csv
sort iris-data-clean.csv | uniq -c | \
cut -f 2 -d '-' iris-data-clean.csv | uniq -c
```

```
 50 setosa
 48 versicolor
 50 virginica
  1 
```

## III. Summarize sequence data file
Find how many lines in the data file `test.fastq,gz` start with "TGCAG" and end with "GAG"

First use the `cat` command to read the file. Then use the `grep` count command to report the number of times the required pattern has been matched for the file. (Referencing the website https://www.cyberciti.biz/faq/howto-use-grep-command-in-linux-unix/). The pattern is expressed using the regular expressions (Referencing the website https://ryanstutorials.net/linuxtutorial/grep.php).

```
cat test.fastq | \
grep -c '^TGCAG.*GAG$'
```

```
44
```

## IV. Summarize sequence data file
Write a `for-loop` to separate the reads from the `file test.fastq.gz` based on the taxon name in the label, and write the reads to separately named files in the new directory called `sorted_reads/`. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the `grep` command that can be used to select multiple lines.

First of all, I created a new directory `sorted_reads/` using the `mkdir` command. Then I created the new files based on the taxon name using the `for-loop` with multiple commands like `grep` `cut` `sort` `uniq` to separate out the taxon and `touch` command within the loop to create the new files (Referencing the website https://ryanstutorials.net/linuxtutorial/grep.php). Second, I need to put respective lines into the given files with the same taxon. I did that by creating another `for-loop` with the command `grep -A1` that got the taxon line and the line below it into the file (Referencing the website https://askubuntu.com/questions/27838/how-to-grep-2-or-3-lines-one-containing-the-text-i-want-and-the-others-just-be).

```
mkdir sorted_reads
for line in $(cat test.fastq | grep '^@[0-9]' | cut -d '.' -f 1 | \
cut -d '_' -f 2 | sort | uniq)
do
  touch sorted_reads/$line
done
cp test.fastq sorted_reads
cd sorted_reads
for textfile in *
do
  grep -A1 $textfile test.fastq > $textfile
done
ls -l
```
```
-rw-r--r--  1 Sylvia  staff   29855 Jan 29 02:24 cyathophylla
-rw-r--r--  1 Sylvia  staff  176365 Jan 29 02:24 cyathophylloides
-rw-r--r--  1 Sylvia  staff  219579 Jan 29 02:24 przewalskii
-rw-r--r--  1 Sylvia  staff  360807 Jan 29 02:24 rex
-rw-r--r--  1 Sylvia  staff   25369 Jan 29 02:24 superba
-rw-r--r--  1 Sylvia  staff       0 Jan 29 02:24 test.fastq
-rw-r--r--  1 Sylvia  staff       0 Jan 29 02:24 thamno
```


  




