# Configure a Github Account With 2FA Enabled

- Install required packages :

```bash
$ sudo apt-get install -y git
```

- Execute the following command : 

```bash
$ git config --global --edit
```

- Edit the file as follows :

```toml
[user]
  name = <your_username>
  # generate PAT here: https://github.com/settings/tokens
  password = <your_pat>
[credential]
  helper = cache
```

- Save file and exit.
