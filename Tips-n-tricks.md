## favico/robots.txt copy

For all the content you have that you don't want generated or dropped into a static folder like your favicon, robots, or other files, just create a folder called `extras` next to your Makefile[^MAKE] then edit your makefile to look like this.

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

[^MAKE]: you did use pelican-quickstart to get an awesome makefile right?

***
## Make vs Rake vs Fabric

Make is universal to almost any *nix but is old and can be clunky for building anything other than code. Many people prefer to use Rake (ruby make) or Fabric (A Pythonic tool for remote execution and deployment). Please post your examples and tips below for awesome development and deployment testing.

**good stuff goes here**

***
