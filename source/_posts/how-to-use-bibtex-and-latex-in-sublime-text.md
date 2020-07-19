---
title: How to Use Bibtex and Latex in Sublime Text
date: 2019-07-26 10:41:16
tags: Latex
categories: Writing
---

Before using *Latex* in **Sublime text 3**, latex release should be installed in your computer. TeX Live in an
excellent release version which can be instlled in Unix, Windows and Mac. Please read documents in its website to install TeX Live.

Download link: [TeX Live](https://www.tug.org/texlive/acquire-netinstall.html)

# Latex Tools

In order to use TeX Live in sublime text 3, a plugin named **LaTeXTools** should be installed first. Use `ctrl+shift+P` to open command line
in sublime text 3 and use the `package control` plugin to instll **LaTexTools**.

Open `LaTexTools.sublime-settings` and find `windows` setting in it to change `texpath` variable according to you TeXLive installed path.

Finally, use `Build With: LaTeX - XeLaTeX` to create PDF file.

# Bibtex

## Knowledge Base

Firstly, we can install `CiteBibtex` plugin in sublime text 3 to manage our knowledge base. we can Create a file named `ciation.bib` and add its path
to 'CiteBibtex.sumlime-setting' for saving paper.

## Insert References in LaTeX

To insert references in LaTeX documents, a file named `ciation.bib` should be added in the same folder as `paper.tex`. Next, in the place
where you would like to insert references, use command `cite{}` to cite references and add two commands about bibtex to `paper.tex` as follow:

```latex
\bibliographystyle{plain}
\bibliography{ciation}
```

Finally, use `Build With: LaTeX - XeLaTeX` to create PDF file.

# References

- [用Sublime Text 3 管理文献](blog.baoduge.com/Use_ST3_manage_reference/)
- [Latex技巧：插入参考文献](https://www.cnblogs.com/yifdu25/p/8330652.html)