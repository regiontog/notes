```
# Rename origin remote to upstream
git remote rename origin upstream
git remote add origin git@github.com:regiontog/shaderkit.git
git fetch origin
git branch --set-upstream-to=origin/main main

```