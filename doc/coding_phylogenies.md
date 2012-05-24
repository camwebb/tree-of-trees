CODING PHYLOGENIES
==================

_Cam Webb_

(Last edited in 2007)

## INTRODUCTION

### Goal

Much’ phylogenetic information that is published in journals in an
unusable form (PDF graphics).  We will convert this into a
machine-readable format (Newick phylogeny representation), and upload
it to a public database with associated tools (Phylomatic
<http://phylodiversity.net/phylomatic>).  The unit of this database is
a tree with standardized root name and standardized tip names, along
with metadata on the source of the tree.  Sub- or supra-trees can then
be attached to the these nodes to create larger phylogenetic
hypotheses.  This ‘hand-assembled’ large tree method stands alongside
supertree methods and supermatrix methods.  There is debate
surrounding the merits of these three methods, but the hand-assembled
method can in some cases offer pragmatic solutions more rapidly than
the other two methods.

### Similar projects

The largest online repository of trees, TreeBase, obtains trees by
voluntary uploads by the article authors.  Our work will complement
TreeBase by offering trees and metadata for input into TreeBase2.  The
Tree-of-Life web project depends on experts to add resolution at nodes
of the ToL.  Our model offers users the choice of different published
trees (including the complete ToL tree, and the GenBank taxonomy
tree).  Other options for creating large trees include supertree
construction methods, and ‘supermatrix’ methods, building
comprehensive trees from, e.g., GenBank.

### This file

Is a guide to converting trees in article PDFs into Newick, with
metadata.


## WHICH TREE?

Our goal is to capture the tree in a publication most similar to
the ‘true tree.’  The best tree will offer a choice between
resolution and accuracy.  In most cases, authors offer several
trees, using different methods, and/or different genes, but there
is usually a summary tree or author’s ‘favourite’ tree offered; it
is usually the final figure in a paper.  Where a consensus tree is
offered, this will be the first to be encoded.  Our second choice
will be the single tree with the highest maximum likelihood.
Third choice will be a ‘one of X most parsimonious trees.’  See
below on dealing with branch resolution estimates (bootstrap
values).

Of course, all trees in a paper can be encoded, but since our goal
is to cover as much taxonomic ground as possible, we will usually
only encode one tree per paper.


## ENCODING VISUAL TREES INTO NEWICK

### Newick standard

Described in these two web publications:

 * <http://evolution.genetics.washington.edu/phylip/newicktree.html>
 * <http://evolution.genetics.washington.edu/phylip/newick_doc.html>

Note that both allow single-daughter-nodes (see below). One
additional component of the standard frequently in use are notes
attached to the nodes (separate from labels).  These are encoded
in square brackets: [...] after the (optional) branch lengths,
which follow the (optional) labels.  An example of the full
complexity of a Newick tree is:

      ((A_sp:1.1,B2-sp:2.2)clade1:1.0[A comment],c_sp:0.5)root;


### General strategies

While it might appear backwards in this age of tree visualization
software, it can still be fastest to code a tree directly into Newick
format ‘by hand,’ with a text editor, checking the product
frequently in a tree viewer like TreeviewX (it is vital that the
viewer easily show internal node names).  However, with a large tree
it can very helpful to print out the tree and mark it up with
colored pencil.

When starting to code a large tree, one should find the natural
‘deep branches,’ and subdivide the tree into manageable units.
These can be spaced out on the text page, and collected together
later with appropriate additional commas and parentheses.  E.g., a
tree like:

                +--- A    \
            +---+          |
        +---+   +--- B     >  branch one
        |   |              |
      --+   +------- C    /
        |      
        |   +------- D    \
        +---+              |
            |   +--- E     >  branch two
            +---+          |
                +--- F     /

could be coded into:

      (                     <--- type third
      ( (A,B) , C)      <--- type first
      ,                     <--- type third
      ( D, (E,F) )        <--- type second
      );                    <--- type third

The space and returns can be remove later, easily, and some tree
readers can even read trees with these extra characters left in.

### Adding genus nodes

Where all species are monophyletic, add a node label to the clade
that represents the genus.  E.g.:

            +--- A_a       Coded as:
        +---+                 
        |   +--- A_b       ((a_a,a_b),(b_a,b_b));
      --+                 
        |   +--- B_a       then as:
        +---+
            +--- B_b       ((a_a,a_b)a,(b_a,b_b)b);
                                 ^          ^

Note that in some phylogenies the species in a preexisting genus
may be polyphyletic.  In this case, you cannot add a genus label
to the clade that contains only some of the taxa:

            +--- A_a       Coded as:
        +---+                 
        |   +--- A_b       ((a_a,a_b),(a_c,b_b));
      --+                 
        |   +--- A_c       then as:
        +---+
            +--- B_b       ((a_a,a_b),(a_c, (b_b)b ));
                                             ^

### One-daughter nodes

If a study contains single representations of apparently
monophyletic species then additional taxonomic information should
be added:

        Family X     Genus A
        v            v
      --+------+-----+--- A_b (= Species)    coded as:  (((a_b)a )y )x ;
               ^
               Section Y

This enables the tree construction software to match by higher
taxa if the species given in the PDF is not the same as the
species given in a particular query.

### Outgroups

Most published phylogenies include some outgroup taxa than enable
the tree to be rooted in the right place.  These outgroup taxa
should be excluded from our encoded trees, so that the root of our
tree is indeed the root node of the clade specified by the name of
the file/tree.  E.g.:

                +--- A  \
            +---+        |         
            |   +--- B   |  family Z
        +---+            |
        |   +------- C  /             
      --+
        +----------- D_z    (outgroup species)
  
should be coded as ((a,b),c)z; in z_author2001.new  However, the
fact that the coded tree is a subtree from the tree published in
the paper must be noted in the XML metadata file (below).

### Taxon names

All letters (a-z) should be lowercase characters.  This
standardizes the matching in the search engine, but also
reinforces the philosophy that all node names are of equal weight,
even though they may correspond to a traditional family name. 

You may find it easier to encode trees using some Capitals, so
that the names are the same as in the PDF.  This is fine---just
convert them to lowercase using the Tools in Notepad++ or another
editor.

Species names should be lowercase genus and species joined by an
underscore: 

      genusname_speciesname

### Software to use

On Windows: Notepad++, and TreeviewX.  All text files should be
saved with UNIX line-endings (in Notepad++ see Format menu).

## METADATA FILES

We have developed a simple XML schema for encoding the metadata
for this project: ‘pmmd’ for Phylomatic Metadata.  An example of
the XML file is:

      <?xml version="1.0" encoding="utf-8"?>
      <pmmd xmlns="http://svn.phylodiversity.net/tot/pmmd/" version="0.3">
        <phyloname>taxon_other2000</phyloname>
        <rootname>taxon</rootname>
        <source collapsed="N" pruned="N" plucked="N" analysis="MP"
    	    consensus="N">
          <pub>
            <citation>
    	      Other AN. 2000. Phylogeny of taxon. J Foo 12:234-567
            </citation>
            <figure>3b</figure>
          </pub>
        </source>
        <notes>Other information</notes>
        <codedby>John Doe</codedby>
        <codeddate>12 may 2007</codeddate>  
        <newick>(a,(b,c)subclade)taxon;</newick>
      </pmmd>

The RNC schema for version 0.3 of the format is at:

 * <http://svn.phylodiversity.net/tot/pmmd/pmmd.rnc>

As an ‘encoder’ you should copy the template into a text editor,
and change the following components (with examples):

The name of the file and of the tree:

        <phyloname>shorea_jones2000</phyloname>

The name of the root:

        <rootname>shorea</rootname>

Edits performed on the PDF tree.  Were some nodes collapsed (see
below, conservative tree)?  Have some branches been removed or
pruned? Has the tree been plucked from a larger tree (i.e. did
you exclude outgroups?:

        <source collapsed="N" pruned="N" plucked="N" 

The type of analysis that created the tree (MP for single maximum
parsimony tree, ML for single maximum likelihood, BAYES for Baysian):

        analysis="MP"

Whether or not the tree is a consensus tree:
  
        consensus="STRICT">

The source publication, cited in a standard form, with the
figure number:

        <pub>
          <citation>
            Other AN. 2000. Phylogeny of Xyz. Journal of Biology 12:234-567.
          </citation>
          <figure>3b</figure>
        </pub>
        </source>
    
The legend of the Figure:

        <notes>Maximum parsimony analysis of Xyz...</notes>

The name of the coder:

        <codedby>John Doe</codedby>

The date of the coding:

        <codeddate>12 may 2007</codeddate>  

The pmmd file can contain the newick file itself, but for our
purposes it should be saved in a separate file.


## NAMING FILES

The Newick file should be save as:

      taxonname_author2000.new

containing ONLY the newick phylogeny. E.g.:

      ((a,b)c)taxonname;

The associated metadata is called:

      taxonname_author2000.xml

If a second tree is coded from the same paper, use a letter after
the authorDATE string:

      taxonname_author2000b.xml
      taxonname_author2000b.new

## CONSERVATIVE VERSIONS

Every tree is a hypothesis with a certain level of confidence.
There are numerous strategies for including estimate of error in
phylogenetic analyses.  One of the simplest is to try to bracket
results obtained using the ‘real tree’ with two trees, a highly
resolved tree and a more conservative tree with uncertain branches
collapsed back into polytomies.  Our goal is to eventually have
two versions of all trees, a resolved and a conservative tree.  If
we are primarily encoding consensus trees, we are storing single,
fairly conservative trees.  However, in cases where only a fully
resolved parsimony or likelihood tree is given, we should store two
trees, the original tree, and a collapsed tree.  The cutoff for
bootstrap values might be set to 75%.  An example of this
collapsing would be:

             30% bootstrap value
              | 
              v +--- A
            +---+                 
            |   +--- B     coded as (((a,b),c),d); in taxon_author2001.new
        +---+
        |   +------- C                 
      --+
        +----------- D


            +------- A
            |                 
            +------- B     coded as ((a,b,c),d); in taxon_author2001.c.new
        +---+
        |   +------- C                 
      --+
        +----------- D

There are also other situations besides low bootstrap values where
a particular node is considered unreliable, and it can be
collapsed back in this way.  The collapsed version of a single
tree is indicated by a `.c` before the `.new` and `.xml`

