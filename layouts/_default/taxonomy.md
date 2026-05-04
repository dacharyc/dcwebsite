> For AI agents: a documentation index is available at /llms.txt — markdown versions of all pages are available by appending index.md to any URL path.

{{ with .Title }}# {{ . }}{{ end }}
{{ .RawContent }}
{{ if eq .Kind "taxonomy" }}{{ range .Pages }}
## {{ .Title }}
{{ range .Pages }}{{ if not .Draft }}
### [{{ .Title }}]({{ .Permalink }}index.md)

{{ with .Description }}{{ . }}{{ end }}
{{ end }}{{ end }}
{{ end }}{{ else }}{{ range .Pages }}{{ if not .Draft }}
## [{{ .Title }}]({{ .Permalink }}index.md)

{{ with .Description }}{{ . }}{{ end }}
{{ end }}{{ end }}{{ end }}
