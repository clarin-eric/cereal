# cereal
CLARIN's cEntral Repository for in-AppLication documentation

## Guidelines and recommendations

### General guidelines

* Default choice of markup language should be Markdown
* The application must cache the documentation content with a reasonable periodic refresh _at run-time_
* A failed refresh of the cache should *not* invalidate the existing cached copy (i.e. first priority should be availability of the documentation)
* The cache should be preloaded with a copy of the documentation that is included _at build time_  (again prioritizing on availability of the documentation even in case of unavailability of the live source)

### Technology specific

TODO
