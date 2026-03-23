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
