> For AI agents: a documentation index is available at /llms.txt — markdown versions of all pages are available by appending index.md to any URL path.

{{ with .Title }}# {{ . }}{{ end }}
{{ with .Description }}
> {{ . }}
{{ end }}
{{ .RawContent }}
{{- $pages := where .Pages "Draft" false }}
{{- $recent := first 10 $pages }}
{{- $older := after 10 $pages }}

## Latest posts
{{ range $recent }}
### [{{ .Title }}]({{ .Permalink }}index.md)

*{{ .Date.Format "January 2, 2006" }}*{{ with .Params.topics }} · {{ delimit . ", " }}{{ end }}

{{ .RawContent }}
{{ range .Params.images }}
![{{ .alt }}]({{ .src | absURL }})
{{ end }}
{{ end }}
{{- if $older }}
## Older posts
{{ range $older }}
- [{{ .Title }}]({{ .Permalink }}index.md) ({{ .Date.Format "2006-01-02" }})
{{- end }}
{{ end }}
