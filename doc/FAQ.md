# FAQ for Tree-of-trees/Phylomatic database

## Q: Why won't the megatrees (e.g., R20081027.new) open in TreeViewX?

A: The phylomatic database allows single daughter nodes in Newick:
((A,B)C)D which are also allowed by the Newick standard.  TreeView can
also handle these, but seems not to like it when the files are large.
The single daughter nodes can be cleaned out using phylocom, e.g.:

      $ phylocom phylocom cleanphy -e -f R20081027.new > clean.new

The resulting file will open in TreeView.  I have found that ATV
can open the files directly.

## Q: Are the XML files valid?

A: Cam regularly runs: jing -c pmmd/pmmd.rnc trees/*.xml
But... the XML files are hand coded, so errors may creep in once in a while.

