# Enable external access

```
jupyter notebook --generate-config
```

Edit:

```
~/.jupyter/jupyter_notebook_config.py
```

Add:

```
c.NotebookApp.allow_origin = '*'
```

```
c.NotebookApp.ip = '0.0.0.0'
```

