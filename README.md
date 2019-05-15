# Running test servers

## One
  ~$docker run --rm --name pg-one -e POSTGRES_PASSWORD=docker -d -p 5432:5432 -v `pwd`/project-testing/one/data:/var/lib/postgresql/data  postgres
  ~$../../packaging/bin/hockeypuck -config ./config.toml
  ~$tail -f ./hkp.log

## Two
  ~$docker run --rm --name pg-two -e POSTGRES_PASSWORD=docker -d -p 5433:5432 -v `pwd`/project-testing/two/data:/var/lib/postgresql/data  postgres
  ~$../../packaging/bin/hockeypuck -config ./config.toml
  ~$tail -f ./hkp.log

# More info

> Hey Tom,
> 
> Yes you can check it out, a little primer because the project is using a dependency management system I wasn't familiar with (and still have questions about honestly):
> 
> the repo 'github.com/schmorrison/server' has a file called 'dependancies.tsv' where the repo and the git commit to build from is listed. This was modified to use my forked repos (and commits), but should probably be modified to use only my forks where I changed content.
>
> then the project is using a dependency source that sort of acts like a git wrapper called gopkg.in, I am also not familiar with how it works but from what I can gather it appends the repo branch to the repo name allowing a client to retrieve a specific branch.
>
> i modified all my forks to use the other forks i made as dependencies, which should be changed back fairly carefully.
> 
> Building the hockeypuck server:
> - building the project is done by cloning the repo github.com/schmorrison/packaging and running the following commands (after you have the Go runtime installed):
> - ./build-debian.bash
> - export GOPATH=$(pwd)
> - go install github.com/schmorrison/server/cmd/hockeypuck
> 
> I also have a 'testing' repo that will help you get up and running two instances of hockeypuck, and a server to act as the endpoints:
> - First clone github.com/schmorrison/hockeypuck-flowcrypt-testing.
> - cd into hockeypuck-flowcrypt-testing/one and hockeypuck-flowcrypt-testing/two in separate terminals
> - run `../../packaging/bin/hockeypuck -config ./config.toml` in each folder to start the server
> - this wont work unless you have PGSQL databases setup on ports 5432 & 5433(as per the configs in one/ and two/, both instances need a database called hkp to be created inside before hand too. I was using docker, you can find the command in the readme)
> - This will create a set of files for the recon db and a log file in each folder
> - you should be able to access the flowcrypt UI at localhost:11371 (for #1) and localhost:11372 (for #2)
> - lastly you should open another terminal and cd into hockeypuck-flowcrypt-testing/ and start the server by running `go run main.go`, as I mentioned, currently the endpoints are hardcoded strings and point to localhost:8080/***
> - finally if you want to follow the log you will want two more terminals and run a tail on hockeypuck-flowcrypt-testing/{one,two}/hkp.log
> 
> To explain the work, it took quite some doing to find where the reconciliation was actually receiving the public keys from the peers and I had written a really messy implementation that worked fine when uploading directly but didn't work when the key was reconciled by a peer. After reading the code very carefully, and a bunch of trial-and-error I found where the server handler and the peer reconciliation intersected, after I found that I was able to pair down quite a bit of the extraneous function calls I had added and could write quite a concise function as per your spec.
