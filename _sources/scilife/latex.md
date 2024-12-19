(debug-tex)=
# LaTeX & Manuscript Writing

Online LaTeX editors, such as Overleaf, offer convenient, real-time collaboration and cloud-based compilation, but advanced features like handling larger documents or extensive team collaboration often require a paid subscription. In contrast, working locally with a dedicated editor like TeXstudio (see {ref}`LaTeX installation <install-tex>`) allows you to edit and compile documents without depending on an internet connection while writing/compiling and without incurring subscription costs. This page is intended to provide guidance on effectively using LaTeX in a local environment.


## Plaintext and word counting

To estimate the number of words in a LaTeX manuscript using TeXstudio, begin by creating a plain text version of your document. Under the **Tools** menu, select **Convert to Abridged Plaintext**, which opens a new untitled tab containing your text without LaTeX commands. This stripped-down file is also suitable for sharing with collaborators who prefer (richtext) word processors. Next, choose **Analyse Text...** from the Tools menu and click the Count button. TeXstudio will display the number of paragraphs (that is, words) in your text and even provide a frequency analysis of specific terms. This functionality can be valuable for refining the manuscript writing style, for instance by identifying an overuse of transitional words like "however".


## Report writing template

For detailed installation instructions, working with TexStudio, and setting up LaTeX documents, please have a look at our LaTeX thesis template at [https://github.com/Ecohydraulics/latex-thesis-template](https://github.com/Ecohydraulics/latex-thesis-template).

## Bibliography

When working with LaTeX, bibliography management typically involves using a .bib file that contains all your references, which you cite within the `.tex` document using `\cite{...}` (or similar) commands. To transform these citations and references into a properly formatted bibliography, you iteratively run a tool like BibTeX after compiling your LaTeX document. **Note that TexStudio does these iterations automatically during compiling**, so these descriptions only explain why you may see the compiling process restart several times even though you only pressed the "Compile" button once.

BibTeX reads the `.aux` file of a previous compiling of the `.tex` document, locates the relevant entries in the `.bib` file, and then creates a `.bbl` file containing only the cited references, now formatted according to the style rules defined by a bibliography style file ( for instance, a `.bst` file when using BibTeX). These style files control the appearance and arrangement of citations and references, including how authors, titles, and journal names are displayed. Once the `.bbl` file is generated, LaTeX incorporates it into the final PDF document during the next compilation run. The `.bbl` file thus is a fully formatted subset of the `.bib` database, ensuring that the PDF document displays only the actually cited references, arranged and styled according to the chosen formatting guidelines.

To manage your reference database and generate a `.bib` file, we recommend using tools like [Zotero](https://www.zotero.org/) (with additional tips in the {ref}`Zotero Tips <debug-zotero>` section). Our [LaTeX thesis template](https://github.com/Ecohydraulics/latex-thesis-template) provides more guidance on working with Zotero and managing the `.bib` file.
