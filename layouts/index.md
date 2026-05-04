> For AI agents: a documentation index is available at /llms.txt — markdown versions of all pages are available by appending index.md to any URL path.

{{ with index .Site.Params.hero "hero__title" }}# {{ . }}{{ end }}
{{ with index .Site.Params.hero "hero__description" }}
> {{ . }}
{{ end }}
{{ .RawContent }}
{{ if .Site.Menus.main }}## Pages
{{ range .Site.Menus.main }}
- [{{ .Name }}]({{ .URL }}/index.md)
{{ end }}{{ end }}
## Recent Posts
{{ range first 10 .Site.RegularPages }}{{ if not .Draft }}
### [{{ .Title }}]({{ .Permalink }}index.md)

{{ with .Description }}{{ . }}{{ end }}
{{ end }}{{ end }}
