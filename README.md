# postgres-tsearch-bulgarian

This repository contains Bulgarian `ispell` (`affix` and `dict`) and `stopword` dictionaries for full text search in PostgreSQL.

## Credits

### ISpell dictionary

The `ispell` dictionary files (`bulgarian.affix` and `bulgarian.dict`) have been created by the [bgOffice/БГ Офис](http://bgoffice.sourceforge.net/) project for use in OpenOffice and are licensed under LGPL 3.0.

This repository contains a modified version of those files (minor changes) to make them compatible with the format expected by PostgreSQL. The original `ispell` files (`bulgarian.aff` and `bulgarian.dic`) can be downloaded from  http://bgoffice.sourceforge.net/ispell/index.html

### Stop words

The stop words list used in this repository (`bulgarian.stop`) is a modified version of the list published in article *"Searching strategies for the Bulgarian language"* (the list is in Table A.1) by Prof. Jacques Savoy.

## How to install

1. Copy the three files `bulgarian.affix`, `bulgarian.dict` and `bulgarian.stop` to your `$SHAREDIR/tsearch_data/` directory (eg. `C:\Program Files\PostgreSQL\12\share\tsearch_data`). You can determine what your `$SHAREDIR` is by running `pg_config --sharedir`.

2. Execute the following SQL script:

       CREATE TEXT SEARCH CONFIGURATION bulgarian (COPY = simple);
       CREATE TEXT SEARCH DICTIONARY bulgarian_ispell (
           TEMPLATE = ispell,
           DictFile = bulgarian, 
           AffFile = bulgarian, 
           StopWords = bulgarian
       );
       CREATE TEXT SEARCH DICTIONARY bulgarian_simple (
           TEMPLATE = pg_catalog.simple,
           STOPWORDS = bulgarian
       );
       ALTER TEXT SEARCH CONFIGURATION bulgarian ALTER MAPPING FOR asciiword, asciihword, hword, hword_part, word WITH bulgarian_ispell, bulgarian_simple;

3. Make sure its working by running a full text search query.

   A query like this one:

       SELECT to_tsvector('bulgarian', 'текстовете');
              
   should output only the base of the word (`текст`):
   
       `"'текст':1"`
