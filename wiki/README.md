# Wiki de Sergio Cayuqueo

Wiki migrada desde DokuWiki, generada con [Hugo](https://gohugo.io)
y el tema [Book](https://github.com/alex-shpak/hugo-book).

## Local

```bash
git submodule update --init --recursive
git add . && git commit -m "Migración inicial desde DokuWiki"   # antes del server:
                                                                 # enableGitInfo lo necesita
hugo server -D              # http://localhost:1313
```

## Avisos

El plugin `note` de DokuWiki se convirtió al shortcode `hint`:

```
{{% hint info %}}     · nota común
{{% hint success %}}  · consejo
{{% hint warning %}}  · importante
{{% hint danger %}}   · advertencia
```

## Verificar enlaces rotos

```bash
hugo server &
npx linkinator http://localhost:1313 --recurse --silent
```

## Deploy

Ver `.github/workflows/hugo.yml` y/o `.woodpecker.yml`.
Para dominio propio en GitHub Pages, crear `static/CNAME`.
