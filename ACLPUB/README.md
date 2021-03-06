# ACLPUB

This directory contains the procedure to add the proceedings and all the corresponding papers to the ACL Anthology, following the instructions available at https://www.aclweb.org/anthology/info/contrib/

Peer-reviewing was done using EasyChair. So, the script `easy2acl.py` available at https://github.com/acl-org/easy2acl was adapted to our needs.

## Information required

Create the files `info/meta.txt`, `info/papers.tsv`, `info/abstracts.html` (optional), and the folder `pdf` as shown. 

Before running `easy2acl.py`, your file structure should look like this:

    |-- info/meta.txt       # conference metadata
    |-- info/abstracts.html # (optional) to include abstracts
    |-- info/papers.tsv     # list of accepted papers
    `-- pdf
        |-- ${abbrev}_${year}.pdf              # full volume of consolidated PDFs
        |-- ${abbrev}_${year}_frontmatter.pdf  # front matter of proceedings
        |-- ${abbrev}_${year}_paper_1.pdf
        |-- ${abbrev}_${year}_paper_2.pdf
        `-- ...

(where ${abbrev} and ${year} are defined in the `info/meta.txt` file, see below)

All the required information can be retrived from EasyChair or easily provided by you. More details can be found in [Getting data from EasyChair](https://github.com/acl-org/easy2acl/#getting-data-from-easychair).

### info/papers.tsv

Tab-separated file, containing 3 or 4 columns: `paper id`, `authors`, `title`, `initial page` (optional)
It accepts comments and empty lines, as shown in the example.

Example:

    # EAMT 2019 Best Thesis Award --- Anthony C Clarke Award			
    901 John Smith	Nice title	5

    # Research		
    21	John Smith, Peter Nice and Anna Cool	Super-nice Title 	9
    29	Tom Cool and Peter Smith	Really nice title	19
    34	Hao Wang, Wang Lee, Xi Yung and Lu Yu	Super cool title    27
    ...
 
If you produced your proceedings with LaTeX, it generates a temporary file containing the initial page of each section. Assuming that the papers are following the exact same order of the proceedings. The page numbers can be pseudo-automatically generated based on this file, as follows:

    cat proceedings.toc| grep "\\contentsline {section}" | awk -F'}{' '{print $3, "%", $2}' > pages.txt
 
 
### info/abstracts.html (optional)

It can be produced, based on the EasyChair information menu `Administration -> Other Utilities -> List of Accepted Submissions`, and then selecting `Show abstracts`. The generated HTML is well-formated and contains the information bellow.

Example:

    <div class="paper">
    <span class="authors"> ... </span>
    <span class="title">...</span></div>
    <div class="abstract">....</div>
    <div class="paper">
    <span class="authors"> ... </span>
    <span class="title">...</span></div>
    <div class="abstract">....</div>
    ...
    
## running `easy2acl.py`

When you run the script for the first time, create a dummy pdf file for the full volume consolidated PDF file and the front matter proceedings file using the above file naming convention.
We will replace the dummy files later and repeat the procedure.

Run the script:

    $ python3 easy2acl/easy2acl.py

When the script has finished, you will see the following additional files in the `proceedings` folder.

    |-- proceedings/
        `-- cdrom/
            |-- ${abbrev}-${year}.bib     # all bib entries
            |-- ${abbrev}-${year}.pdf     # entire volume
            |-- bib/
            |   |-- {year}.{abbrev}-{volume_name}.0.bib  # frontmatter
            |   |-- {year}.{abbrev}-{volume_name}.1.bib  # first paper
            |   |-- {year}.{abbrev}-{volume_name}.2.bib  # second paper
            |   `-- ...
            `-- pdf/
                |-- {year}.{abbrev}-{volume_name}.0.pdf  # frontmatter
                |-- {year}.{abbrev}-{volume_name}.1.pdf  # first paper
                |-- {year}.{abbrev}-{volume_name}.2.pdf  # second paper
                `-- ...

This is the input format that [the ingestion scripts for the ACL Anthology](https://github.com/acl-org/ACLPUB) expect.

The `easyacl.py` script also creates a file `book-proceedings/all_papers.tex` which contains an index of files that is read in automatically by `book-proceedings.tex`.
This document can be used to generate a full volume consolidated PDF file.
This file contains front matter (which you should edit), and automatically creates a table of contents and includes all papers from `all_papers.tex`.
To create the book proceedings simply edit the front matter and recompile `book_proceedings.tex`.
Copy `book-proceedings.pdf` to `${abbrev}_${year}_frontmatter.pdf` in your `pdf` folder.
To create `${abbrev}_${year}_frontmatter.pdf` extract the front matter pages with roman page numbers from `book-proceedings.pdf`.
Finally, re-run `python3 easy2acl.py` to replace the dummy full book proceedings and front matter files in the `proceedings` folder that you will use in the next step.

Once this data is generated, you can proceed with ACLPUB to generate the XML ingestion file and layout that the Anthology requires.

