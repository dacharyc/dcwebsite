> For AI agents: a documentation index is available at /llms.txt — markdown versions of all pages are available by appending index.md to any URL path.

{{ with index .Site.Params.hero "hero__title" }}# {{ . }}{{ end }}
{{ with index .Site.Params.hero "hero__description" }}
> {{ . }}
{{ end }}
{{ .RawContent }}
{{ if .Site.Menus.main }}## Pages
{{ range .Site.Menus.main }}
- [{{ .Name }}]({{ .URL | absURL }}/index.md)
{{ end }}{{ end }}
## Full Articles
{{ range first 10 (where .Site.RegularPages "Type" "posts") }}{{ if not .Draft }}
### [{{ .Title }}]({{ .Permalink }}index.md)

{{ with .Description }}{{ . }}{{ end }}
{{ end }}{{ end }}
## Microblog

Short posts, syndicated to LinkedIn. Full list: https://dacharycarey.com/microblog/index.md
{{ range first 5 (where .Site.RegularPages "Type" "microblog") }}{{ if not .Draft }}
### [{{ .Title }}]({{ .Permalink }}index.md)

*{{ .Date.Format "January 2, 2006" }}*

{{ .RawContent }}
{{ range .Params.images }}
![{{ .alt }}]({{ .src | absURL }})
{{ end }}
{{ end }}{{ end }}
