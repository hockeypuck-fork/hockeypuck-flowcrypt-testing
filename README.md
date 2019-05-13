# Running test servers

## One
  ~$docker run --rm --name pg-one -e POSTGRES_PASSWORD=docker -d -p 5432:5432 -v `pwd`/project-testing/one/data:/var/lib/postgresql/data  postgres
  ~$../../packaging/bin/hockeypuck -config ./config.toml
  ~$tail -f ./hkp.log

## Two
  ~$docker run --rm --name pg-two -e POSTGRES_PASSWORD=docker -d -p 5433:5432 -v `pwd`/project-testing/two/data:/var/lib/postgresql/data  postgres
  ~$../../packaging/bin/hockeypuck -config ./config.toml
  ~$tail -f ./hkp.log
