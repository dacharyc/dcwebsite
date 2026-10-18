> For AI agents: a documentation index is available at /llms.txt — markdown versions of all pages are available by appending index.md to any URL path.

{{ with .Title }}# {{ . }}{{ end }}
{{ with .Date }}*{{ .Format "January 2, 2006" }}*{{ end }}{{ with .Params.topics }} · {{ delimit . ", " }}{{ end }}

{{ .RawContent }}
{{ range .Params.images }}
![{{ .alt }}]({{ .src | absURL }})
{{ end }}
{{ with .Params.linkedin }}
---

Originally posted on LinkedIn: {{ . }}
{{ end }}
All microblog posts: https://dacharycarey.com/microblog/index.md
