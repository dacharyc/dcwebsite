{{/* Hugo uses taxonomy.md for both taxonomy and term pages in the Markdown output format. */}}
{{/* This file exists for documentation purposes; see taxonomy.md for the actual template. */}}
{{ with .Title }}# {{ . }}{{ end }}
{{ .RawContent }}
{{ range .Pages }}{{ if not .Draft }}
## [{{ .Title }}]({{ .Permalink }}index.md)

{{ with .Description }}{{ . }}{{ end }}
{{ end }}{{ end }}
