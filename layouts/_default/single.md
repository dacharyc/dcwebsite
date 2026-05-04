> For AI agents: a documentation index is available at /llms.txt — markdown versions of all pages are available by appending index.md to any URL path.

{{ with .Title }}# {{ . }}{{ end }}
{{ with .Date }}*{{ .Format "January 2, 2006" }}*{{ end }}

{{ .RawContent }}
