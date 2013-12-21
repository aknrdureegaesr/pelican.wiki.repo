## Copying favicon/robots.txt

### First solution

If you used the `pelican-quickstart` command to create a Makefile and want certain files copied to your web root — such as `favicon.ico`, `robots.txt`, or other files — create a folder called `extra` next to your Makefile and edit your Makefile to look like this:

```
 $(OUTPUTDIR)/%.html:
	$(PELICAN) $(INPUTDIR) -o $(OUTPUTDIR) -s $(CONFFILE) $(PELICANOPTS)
	if test -d $(BASEDIR)/extra; then cp $(BASEDIR)/extra/* $(OUTPUTDIR)/; fi
```
```
publish:
	$(PELICAN) $(INPUTDIR) -o $(OUTPUTDIR) -s $(PUBLISHCONF) $(PELICANOPTS)
	if test -d $(BASEDIR)/extra; then cp $(BASEDIR)/extra/* $(OUTPUTDIR)/; fi
```

### Second solution, using `FILES_TO_COPY`

Assuming that you have `favicon.ico` and `robots.txt` in the `content/extra/` folder, just add the  next lines to `pelican.conf`:

```python
FILES_TO_COPY = (('extra/robots.txt', 'robots.txt'),
                 ('extra/favicon.ico', 'favicon.ico'),)
```

## Make vs Rake vs Fabric

Make is available on almost any Unix-derived system but is old and can be clunky for building anything other than code. Many people prefer to use Rake (ruby make) or [Fabric][] (a Pythonic tool for remote execution and deployment). Please post your examples and tips below for awesome development, testing, and deployment.

### make newpost

You can edit your Makefile to give you a handful of new commands that might make it slightly easier to get writing.  Just add the following lines to your Makefile:

```make
PAGESDIR=$(INPUTDIR)/pages
DATE := $(shell date +'%Y-%m-%d %H:%M:%S')
SLUG := $(shell echo '${NAME}' | sed -e 's/[^[:alnum:]]/-/g' | tr -s '-' | tr A-Z a-z)
EXT ?= md

newpost:
ifdef NAME
	echo "Title: $(NAME)" >  $(INPUTDIR)/$(SLUG).$(EXT)
	echo "Slug: $(SLUG)" >> $(INPUTDIR)/$(SLUG).$(EXT)
	echo "Date: $(DATE)" >> $(INPUTDIR)/$(SLUG).$(EXT)
	echo ""              >> $(INPUTDIR)/$(SLUG).$(EXT)
	echo ""              >> $(INPUTDIR)/$(SLUG).$(EXT)
	${EDITOR} ${INPUTDIR}/${SLUG}.${EXT} &
else
	@echo 'Variable NAME is not defined.'
	@echo 'Do make newpost NAME='"'"'Post Name'"'"
endif

editpost:
ifdef NAME
	${EDITOR} ${INPUTDIR}/${SLUG}.${EXT} &
else
	@echo 'Variable NAME is not defined.'
	@echo 'Do make editpost NAME='"'"'Post Name'"'"
endif

newpage:
ifdef NAME
	echo "Title: $(NAME)" >  $(PAGESDIR)/$(SLUG).$(EXT)
	echo "Slug: $(SLUG)" >> $(PAGESDIR)/$(SLUG).$(EXT)
	echo ""              >> $(PAGESDIR)/$(SLUG).$(EXT)
	echo ""              >> $(PAGESDIR)/$(SLUG).$(EXT)
	${EDITOR} ${PAGESDIR}/${SLUG}.$(EXT)
else
	@echo 'Variable NAME is not defined.'
	@echo 'Do make newpage NAME='"'"'Page Name'"'"
endif

editpage:
ifdef NAME
	${EDITOR} ${PAGESDIR}/${SLUG}.$(EXT)
else
	@echo 'Variable NAME is not defined.'
	@echo 'Do make editpage NAME='"'"'Page Name'"'"
endif
```

To use this, make sure you've set the EDITOR environment variable to the name of your favourite editor, (or set it again within the Makefile), and then do
```bash
$ make newpost NAME='Your Exciting Post Name Here'
```
Your editor will appear with the beginnings of a new post.  You can set 'EXT' to whatever extension you use most often, and then overwrite it by passing `"EXT='newext'"` to make.  Equally, this change assumes that all your pages are stored in `content/pages`, which may not be true for you.  This sample should work well enough for you to base any modifications off, though.


[Fabric]: http://www.fabfile.org/

## Generate `sitemap.xml`

This idea has come to me from Django framework. For generating `sitemap.xml` we will use a Jinja2 template.
First, you should add `sitemap.html` to templates folder of your theme with the following content:

```html
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">

{% for article in articles %}
  <url>
    <loc>{{ SITEURL }}/{{ article.url }}</loc>
    <priority>0.8</priority>
  </url>

  {% for translation in article.translations %}
  <url>
    <loc>{{ SITEURL }}/{{ translation.url }}</loc>
    <priority>0.8</priority>
  </url>
  {% endfor %}
{% endfor %}

{% for page in pages %}
  <url>
    <loc>{{ SITEURL }}/{{ page.url }}</loc>
    <priority>1.0</priority>
  </url>

  {% for translation in page.translations %}
  <url>
    <loc>{{ SITEURL }}/{{ translation.url }}</loc>
    <priority>1.0</priority>
  </url>
  {% endfor %}
{% endfor %}

</urlset>
```

Next, you should add this template to the list with direct templates.
So make sure that `DIRECT_TEMPLATES` tuple in your `settings.py` contains `sitemap`.

```python
# default value is ('index', 'tags', 'categories', 'archives')
# so we just add a 'sitemap'
DIRECT_TEMPLATES = ('index', 'tags', 'categories', 'archives', 'sitemap')
```

Finally, you should set `SITEMAP_SAVE_AS` variable for saving result as 'sitemap.xml'.

```python
SITEMAP_SAVE_AS = 'sitemap.xml'
```

## pelican-mode for Emacs

https://github.com/joewreschnig/pelican-mode

pelican-mode has commands for generating post files, as well as rebuild and uploading sites from emacs.

## Arbitrary Page Ordering

As long as [#420](https://github.com/getpelican/pelican/issues/420) isn't resolved, you can get your pages (or articles, or whatever) in an arbitrary order of your choice with two little tricks:
 * In the for loop rendering out your menu, add a sort filter to sort by filename:
```python
      {% for p in PAGES|sort(attribute='filename') %}
          <li{% if p == page %} class="active"{% endif %}><a href="{{ SITEURL }}/{{ p.url }}">{{ p.title }}</a></li>
      {% endfor %}
```
 * Prepend your page file names with numbers, e.g. 00about.rst, 10projects.rst and so on.
This will get the pages in the order of your choice without messing up your slug. (Default page ordering is putting a case-sensitive alphabetical order by slug, in case you wondered.)