# Renmin OCR/NER Collection Creation

Version 1.0

The Renmin OCR/NER Collection supports evaluation of named entity recognition (NER) over optical character recognition (OCR) output. Once constructed, the collection consists of a set of pdf newspaper pages from Renmin Ribao, together with character-tokenized versions of the articles in those pages annotated with fifteen named entity types.

To create the collection, you will need to download the source pdf files from Renmin, then replace the coded tokens in the annotation files with actual tokens recovered from the downloaded pdf files. This somewhat tortuous process is necessary because we do not have the rights to distribute the Renmin source data. Fortunately, the attached scripts should do all of the work for you.

## Requirements

To recreate the OCR/NER data you will need:

* This distribution, which contains:
    * This README
    * Annotation files (```train.encoded.txt```, ```dev.encoded.txt``` and ```test.encoded.txt```) in which token strings have been encoded using keys in the original pdf file. This ensures that the text cannot be recreated without access to the pdfs. These encoded annotation files are in the ```data``` directory.
    * The ```create-renmin-collection.py``` python3 program that converts encoded tokens back to actual token strings. This program, together with the two other scripts it calls (```renmin-downloader.py``` and ```renmin-reconstructor.py```) are in the ```code``` directory.

* The Renmin source data are available from
[the Renmin Ribao Web Site](http://paper.people.com.cn/rmrb). You will need http connectivity to this site.

* You will need Python 3.6 or later to run the scripts in this distribution.

* To generate images of the newspaper pages, you will need the ability to convert from pdf to the image format of your choice. We assume a DPI of 216; if you use a different DPI, you will need to convert the offsets in the output files.

## Building the Collection

The ```create-renmin-collection.py``` program will download the Renmin pdf pages used by the collection, and use them to decode the tokens listed in the encrypted train, dev, and test files. This script takes a single argument, the name of the directory into which the collection is to be placed. The scripts use relative paths to find the location of the encoded files and the other scripts, so they should not be moved from the directory in which they were created.

The CoNLL files created by ```create-renmin-collection.py``` should have the following MD5 checksums:

File                  | MD5 Checksum
----------------------|-------------
```train.conll.txt``` | ```4ba2f972324ad9e11ee81f4991b41492```
```dev.conll.txt```   | ```670e50e9d71e76f0a1154f542892f7ff```
```test.conll.txt```  | ```6ee5a3a892900b15f41a411973aad112```



We leave it to the user of the collection to convert the pdf files to images in a form that meets their needs; this ensures that the image file format, DPI, etc., work with the user's OCR system.

## Data Format

The files created through this process (```train.conll.txt```, ```dev.conll.txt```, and ```test.conll.txt```) are tab-separated, UTF-8-encoded text files. Sentences are separated by blank lines. The columns, from left to right, are:

1. Token (e.g., '合' or ','). The data are tokenized by character, so a word might span more than one token.
2. Tag (e.g., ```B-PER``` or ```O```). Tags are in what Wikipedia calls ["IOB2" format](https://en.wikipedia.org/wiki/Inside%E2%80%93outside%E2%80%93beginning_(tagging)).
3. Unique token ID (e.g., ```Renmin_2018_06_01_02-128-9```). This ID encodes the year, month, day, page number, box number, and token number counts left to right (or top to bottom) within the box.
4. ID of previous token in reading order (e.g., ```Renmin_2018_06_01_02-130-17``` or ```None```). Because the layout is drawn from a printed newspaper, the previous token is not always the immediately preceding token in the file.
5. xmin (e.g., "276.995563"). This and the following three fields are the coordinates of the box tightly surrounding the line containing the token on the page assuming a DPI of 216. (This means several tokens may have the same location. The token number indicates the token offset within the line)
6. ymin
7. xmax
8. ymax

Note that although we refer to these files as CoNLL files (after the format used in the CoNLL 2002 and 2003 shared tasks), the term *CoNLL file* has been overloaded in the literature and now is used to refer to a family of related formats. In particular, the original CoNLL files do not conform to the specification given here.

## Tag Set

All named entities are tagged with a type in this dataset, with MISC serving as the type for all entities that do not fit into one of the other categories. Any token that is not part of a named entity mention is tagged with the 'O' tag.

Type     | Description            | Examples
---------|------------------------|----------
PER      | Person                 | Enrico Rastelli, Beyoncé
ORG      | Organization           | International Jugglers Association
COMM     | Commercial Org.        | Penguin Magic, Papermoon Diner
POL      | Political Organization | Green Party, United Auto Workers
GPE      | Geo-political Entity   | Morocco, Carlisle, Côte d'Ivoire
LOC      | Natural Location       | Kartchner Caverns, Lake Erie
FAC      | Facility               | Stieff Silver Building, Hoover Dam
GOVT     | Government Building    | White House, 10 Downing St.
AIR      | Airport                | Ninoy Aquino International, BWI
EVNT     | Named Event            | WWII, Operation Desert Storm
VEH      | Vehicle                | HMS Titanic, Boeing 747
COMP     | Computer Hard/Software | Nvidia GeForce RTX 2080 Ti, Reunion
WEAP     | Weapon                 | Fat Man, 13"/35 caliber gun 
CHEM     | Chemical               | Iron, NaCl, hydrochloric acid
MISC     | Other named entity     | Dark Star, Lord of the Rings

## README Translation

If anyone cares to create a Chinese version of this README that is better than the output of Google Translate, we will add it to the repository.

## Citation

If you use this collection, please cite:

Dawn Lawrie, James Mayfield and David Etter, "Building OCR/NER test collections." In *Proceedings of LREC 2020*, Marseille, 2020.
