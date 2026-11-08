# dknr-domotic.github.io

Sources du site de documentation des projets DKNR : https://dknr-domotic.github.io

Le site est construit avec [Zensical](https://zensical.org/) (successeur de Material for MkDocs, configuration dans `mkdocs.yml`) et publié sur GitHub Pages à chaque push sur `master` par le workflow `.github/workflows/deploy.yml`.

## Modifier le site

Les pages sont des fichiers Markdown dans `docs/`. Le menu est défini par `nav` dans `mkdocs.yml`.

Pour voir le site en local :

```bash
pip install zensical
zensical serve
```

Le site est alors disponible sur http://localhost:8000.
