## Copying favicon/robots.txt

If you used the `pelican-quickstart` command to create a Makefile and want certain files copied to your web root — such as favicon.ico, robots.txt, or other files — create a folder called `extras` next to your Makefile and edit your Makefile to look like this:

```
 $(OUTPUTDIR)/%.html:
	$(PELICAN) $(INPUTDIR) -o $(OUTPUTDIR) -s $(CONFFILE) $(PELICANOPTS)
	cp $(BASEDIR)/extras/* $(OUTPUTDIR)/
```
```
publish:
	$(PELICAN) $(INPUTDIR) -o $(OUTPUTDIR) -s $(PUBLISHCONF) $(PELICANOPTS)
	cp $(BASEDIR)/extras/* $(OUTPUTDIR)/
```

or just add to `pelican.conf` next lines:

```
FILES_TO_COPY = (('extra/robots.txt', 'robots.txt'),
                 ('extra/favicon.ico', 'favicon.ico'),)
```

## Make vs Rake vs Fabric

Make is available on almost any Unix-derived system but is old and can be clunky for building anything other than code. Many people prefer to use Rake (ruby make) or [Fabric][] (a Pythonic tool for remote execution and deployment). Please post your examples and tips below for awesome development, testing, and deployment.

**(good stuff goes here)**


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