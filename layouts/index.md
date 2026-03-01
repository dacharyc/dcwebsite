{{ with .Title }}# {{ . }}{{ end }}
{{ with .Site.Params.description }}
> {{ . }}
{{ end }}
{{ .RawContent }}
{{ if .Site.Menus.main }}## Pages
{{ range .Site.Menus.main }}
- [{{ .Name }}]({{ .URL }}/index.md)
{{ end }}{{ end }}
