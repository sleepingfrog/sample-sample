# docker dev(experimental)

###
docker compose -f .devcontainer/compose.yml up -d db redis
docker compose -f .devcontainer/compose.yml run rails bash

bundle exec rails dbconsole
# mysql container(Oracle Linux Server)に入ってmysqlクライアントを使うと日本語が入力できない
# rails container(debian)からmysqlクライアントでmysqlcontainerにつないで試す
