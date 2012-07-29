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