---
title: Setup
---

As yesterday, we need to download the training materials from the GitHub code repository online. Go to `https://github.com/sabs-r3/module01_se_day2` in a browser and select the green `Clone or download` button, and then select `Download ZIP`. This will download all the files within a single archive file. After it's finished downloading, we need to extract all files from the archive. Find where the file has been downloaded to, then start a terminal, and assuming the file has downloaded to e.g. `/home/sabs-r3/Downloads`, do the following within the shell:

~~~
cd ~
unzip /home/sabs-r3/Downloads/module01_se_day2-gh-pages.zip
~~~
{: .language-bash}

This will unpack the archive in your home directory, within a subdirectory called `module01_se_day2-gh-pages`. Change to the `code` directory within that new directory:

~~~
cd module01_se_day2-gh-pages/code
~~~
{: .language-bash}

{% include links.md %}
