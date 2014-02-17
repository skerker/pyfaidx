[![Build Status](https://travis-ci.org/mdshw5/pyfaidx.png?branch=master)](https://travis-ci.org/mdshw5/pyfaidx)

## Description

Samtools provides a function "faidx" (FAsta InDeX), which 
creates a small flat index file ".fai" allowing for fast random access to any 
subsequence in the indexed fasta, while loading a minimal amount of the file
in to memory.

Pyfaidx provides an interface for creating and using this index for fast 
random access of **DNA** subsequences from huge fasta files 
in a "pythonic" manner. For example:

```python
>>> from pyfaidx import Fasta
>>> genes = Fasta('tests/data/genes.fasta')
>>> genes
Fasta("tests/data/genes.fasta")
```

Acts like a dictionary:
```python
>>> genes.keys()
['NR_104215.1', 'KF435150.1', 'NM_001282548.1', 
 'NM_001282549.1', 'XM_005249644.1', 'NM_001282543.1', 
 'NR_104216.1', 'XM_005265508.1', 'XR_241079.1', 
 'AB821309.1', 'XM_005249645.1', 'XR_241081.1', 
 'XM_005249643.1', 'XM_005249642.1', 'NM_001282545.1', 
 'NR_104212.1', 'XR_241080.1', 'XM_005265507.1', 
 'KF435149.1', 'NM_000465.3']
 
>>> genes['NM_001282543.1'][200:230]
>NM_001282543.1:201-230
CTCGTTCCGCGCCCGCCATGGAACCGGATG

>>> genes['NM_001282543.1'][200:230].seq
'CTCGTTCCGCGCCCGCCATGGAACCGGATG'
>>> genes['NM_001282543.1'][200:230].name
'NM_001282543.1:201-230'
>>> genes['NM_001282543.1'][200:230].start
201
>>> genes['NM_001282543.1'][200:230].end
230
```

Slices just like a string:
```python
>>> genes['NM_001282543.1'][200:230][:10]
>NM_001282543.1:201-210
CTCGTTCCGC

>>> genes['NM_001282543.1'][200:230][::-1]
>NM_001282543.1:230-201
GTAGGCCAAGGTACCGCCCGCGCCTTGCTC

>>> genes['NM_001282543.1'][200:230][::3]
>NM_001282543.1:201-230
CGCCCCTACA
```

Complements and reverse complements just like DNA
```python
>>> genes['NM_001282543.1'][200:230].complement
>NM_001282543.1 (complement):201-230
GAGCAAGGCGCGGGCGGTACCTTGGCCTAC

>>> -genes['NM_001282543.1'][200:230]
>NM_001282543.1 (complement):230-201
CATCCGGTTCCATGGCGGGCGCGGAACGAG
```
    
It also provides a command-line script:

### cli script: faidx

```shell
$ faidx tests/data/genes.fasta NM_001282543.1:201-210 NM_001282543.1:300-320
>NM_001282543.1:201-210
CTCGTTCCGC
>NM_001282543.1:300-320
GTAATTGTGTAAGTGACTGCA


Same syntax as `samtools faidx`
```shell
$ samtools faidx tests/data/genes.fasta NM_001282543.1:201-210 NM_001282543.1:300-320
>NM_001282543.1:201-210
CTCGTTCCGC
>NM_001282543.1:300-320
GTAATTGTGTAAGTGACTGCA
```

A lower-level Faidx class is also available:

```python
>>> from pyfaidx import Faidx
>>> fa = Faidx('T7.fa')
>>> fa.build('T7.fa', 'T7.fa.fai')
>>> fa.index
{'EM_PHG:V01146': {'lenc': 60, 'lenb': 61, 'rlen': 39937, 'offset': 40571}, 'EM_PHG:GU071091': {'lenc': 60, 'lenb': 61, 'rlen': 39778, 'offset': 74}}

>>> fa.fetch('EM_PHG:V01146', 1, 10)
EM_PHG:V01146
TCTCACAGTG

>>> fa.fetch('EM_PHG:V01146', 100, 120)
>EM_PHG:V01146
GGTTGGGGATGACCCTTGGGT
```
    
- If the FASTA file is not indexed, when `Faidx` is initialized the `build` method will automatically run,
producing "filename.fa.fai" where "filename.fa" is the original FASTA file.
- Start and end coordinates are 1-based.

## Installation

This package is tested under Python 3.3, 3.2, 2.7, 2.6, and pypy.

```
pip install pyfaidx

or

python setup.py install
```

## CLI Usage

"samtools faidx" compatible FASTA indexing in pure python.

    usage: faidx [-h] [-n] fasta [regions [regions ...]]
    
    Fetch sequence from faidx-indexed FASTA
    
    positional arguments:
      fasta       FASTA file
      regions     space separated regions of sequence to fetch e.g. chr1:1-1000
    
    optional arguments:
      -h, --help  show this help message and exit
      -n, --name  print sequence names

Acknowledgements
------------------
This project is freely licensed by the author, [Matthew Shirley](http://mattshirley.com), and was completed under the mentorship 
and financial support of Drs. [Sarah Wheelan](http://sjwheelan.som.jhmi.edu) and [Vasan Yegnasubramanian](http://yegnalab.onc.jhmi.edu) at 
the Sidney Kimmel Comprehensive Cancer Center in the Department of Oncology.
