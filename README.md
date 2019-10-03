# ENSEMBL_TO_HUGO

The aim of this program is to convert a comma delimited file that cotains the gene ID in the ENSEMBL format to the HUGO format. 

##Installation of Required Data Sets: 

This program requires two data sets. 
The first being the Homo_sapiens.GRCh37.75.gtf file found on the link: http://ftp.ensembl.org/pub/release-75/gtf/homo_sapiens 
  To get the file from this link, you must use the funtion wget while inside your working directory. 
  The file will be zipped, so you must unzip it using the gunzip command
  The script in the command line would look something like this: 
```
wget Homo_sapiens.GRCh37.75.gtf.gz
gunzip Homo_sapiens.GRCh37.75.gtf.gz
```
The second data set can be found in the following link: https://github.com/davcraig75/unit
  To get the file, first access the url that contains the raw data. 
  Using the command of git clone, you can download the data set into your local directory. 
  The script in the command line would look something like this: 
```
git clone https://raw.githubusercontent.com/davcraig75/unit/master/expres.anal.csv
```
  The data will be saved as a file within a directory called 'unit' 
  
##Understanding the Script:
First, the required function modules are called upon in the header of the script. The file that is utilized as a library is called in the first argument and a for loop is used to iterate through the lines of the file.
```
import sys
import fileinput
import re
import json


my_file=([Homo_sapiens.GRCh37.75.gtf])

Lookup_geneID={}
for each_line_of_text in fileinput.input(my_file):
```
Using regular expressions, the gene id and gene name are extracted from the lines of the text and stored under new variables. The text is split into columns by using the regex function of 're.split'. Upon accessing each column, the values present in the gene id is assigned to gene name in a Lookup dictionary. 
```
 gene_id_matches = re.findall('gene_id \"(.*?)\";',each_line_of_text)
        gene_name_matches = re.findall('gene_name \"(.*?)\";',each_line_of_text)
        text_in_columns = re.split('\t',each_line_of_text)
        if len(text_in_columns)>3:
                if text_in_columns[2] == "gene":
                        if gene_id_matches:
                                if gene_name_matches:
                                        Lookup_geneID[gene_id_matches[0]] = gene_name_matches[0]
```
The file to be changed is then accessed from the second argument in the command line. The header is printed in the new file. A for loop is used to access each line in the new file. The files contents are split into columns using the split function. 
The function 're.sub' substitutes one value with another. In this case the column containing the regular expression 'ENSG' followed by a number is replaced with the the column containing the value of the gene_name. 
The new line is then printing 
The option is acessed by using '$1' location. If it contains '=f2' then the replacement occurs and prints to the new file. 
```
file_to_change=sys.argv[2]
print "gene_id\tgene_type\tlogFC\tAveExpr"
for line in fileinput.input(file_to_change):
        text_in_columns=re.split('\t',line)
        print re.sub (r'gene_id \"(.*?)\";',r'ENSG\d*.',line)
        if text_in_columns[0] in Lookup_geneID:
                print Lookup_geneID[text_in_columns[0]] + "\t" +text_in_columns[1] + "\t" +text_in_columns[2] + "\t" +text_in_columns[4]
        if "$1" == "-f2":
                text_in_columns[0]=re.sub(r 'ENSG\d*.','gene_name\"(.*?)\";', text_in_columns)
                print Lookup_geneID[text_in_columns[0]] + "\t" +text_in_columns[1] + "\t" +text_in_columns[2] + "\t" +text_in_columns[4]
```
