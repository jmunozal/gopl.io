# The Go Programming Language

This repository provides the downloadable example programs
for the book, "The Go Programming Language"; see http://www.gopl.io.

These example programs are licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.<br/>
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png"/></a>

You can download, build, and run the programs with the following commands:

	$ export GOPATH=$HOME/gobook            # choose workspace directory
	$ go get gopl.io/ch1/helloworld         # fetch, build, install
	$ $GOPATH/bin/helloworld                # run
	Hello, 世界

Many of the programs contain comments of the form `//!+` and `//!-`.
These comments bracket the parts of the programs that are excerpted in the
book; you can safely ignore them.  In a few cases, programs
have been reformatted in an unnatural way so that they can be presented
in stages in the book.

# Go container k8s and google cloud (ch1/server3)

## Set up a k8c cluster

Based on Marko Luska _Kubernetes in action_

1. Install kubectl and Google Cloud SDK 
2. Sign up for a Google account
3. In https://console.cloud.google.com/, create a project
4. Change to the project created in (2) and enable k8s api
5. Login using _gcloud_

```
gcloud auth login
```
## Create docker image

in ch1/server3 folder

```
docker build -t klusty .
```
## Push image to registry

```
MacBook-MacBook-Pro-de-Jesus-3:server3 kepler$ docker tag klusty jmunozal/klusty
MacBook-MacBook-Pro-de-Jesus-3:server3 kepler$ docker push jmunozal/klusty
The push refers to repository [docker.io/jmunozal/klusty]
a9b500dcf624: Pushed 
d4afe79d29fd: Pushed 
8b1f1d98a924: Pushed 
bf8dd312214d: Pushed 
387f402927ce: Pushed 
f4907c4e3f89: Pushed 
b17cc31e431b: Pushed 
12cb127eee44: Pushed 
604829a174eb: Pushed 
fbb641a8b943: Pushed 
latest: digest: sha256:dff463b42166ec221267cff2639ca905295312c304dfa13a1a099ffa38575c10 size: 2420
```

## Create a k8s cluster (you can start from here!)

Don't forget to remove it after your tests!

```
gcloud container clusters create klusty --num-nodes 3 --machine-type f1-micro --zone europe-west1-b
```

## Create replicationcontroller 

```
$kubectl run klusty --image jmunozal/klusty --port 8000 --generator=run/v1
replicationcontroller "klusty" created
```

## Expose your service

```
$kubectl expose rc klusty --type=LoadBalancer --name klusty-http
service "klusty-http" exposed
```

Service is now accessible:

```
$kubectl get services
NAME          TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
klusty-http   LoadBalancer   10.15.244.227   35.195.198.157   8000:31675/TCP   47s
kubernetes    ClusterIP      10.15.240.1     <none>           443/TCP          10m

$curl http://35.195.198.157:8000
GET / HTTP/1.1
Header["User-Agent"] = ["curl/7.54.0"]
Header["Accept"] = ["*/*"]
Host = "35.195.198.157:8000"
RemoteAddr = "10.12.0.1:50788"