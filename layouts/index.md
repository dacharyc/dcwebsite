{{ with .Title }}# {{ . }}{{ end }}
{{ with .Site.Params.description }}
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
