---
title: "Containerizing Application"
weight: 101
params:
  bookCollapseSection: true
---

## Goal: 
- Create a Containerfile for Explore California
- Create a container image form that Containerfile
- Creta a container from that container image

## Source:

{{% columns %}}
- {{< image src="./1.jpg" >}}
- {{< image src="./2.jpg" >}}
{{% /columns %}}

### Create the **Dockerfile**

```ps
FROM nginx:alpine
LABEL maintainer="Your Name <your@email.address>"
COPY website /website
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
```

{{% details title="Details" open=false %}}
  - **FROM nginx:alpine**->  instruct Podman or Docker through creating a container images.
  - **LABEL maintainer="Your Name <your@email.address>"** -> The label command underneath it adds useful information to your container images like the maintainer property shown here.
  - **COPY website /website** -> copies files within the container image builder's context into container images like **nginx.conf**or the website directory
  - **EXPOSE 80** -> tells users of the container image, which network port they should expect the application to be served from within the container. 
{{% /details %}}

### Building your image with Podman

```
podman build -t explorecalifornia.com .
```

{{% details title="Details" open=false %}}

- `-t` tells podman to give our container image a name, like explorecalifornia over here. While this option is optional, I highly recommend using it as it'll be really difficult to find the image that you created otherwise.
- `.` The dot at the end is not a typo. This is the directory to provide to the builder as context. You can think of context as a file system of files and directories that can be copied into the container image while it's being built. 
  - The dot here is short for the directory we're currently in, or our current working directory. You might see this dot represented as $**PWD**, or with something like the pwd command in here [**"$(pwd)"**]. But most times you'll see it referenced as a dot. 
{{% /details %}}


{{< image src="./p1.jpg" >}}
{{< image src="./p2.jpg" >}}

- Checking command: `podman help`

### Testing your changes

``` 
podman run --rm --name explorecalifornia.com -p 5000:80 explorecalifonia.com
```
{{% details title="Details" open=false %}}
- `run` like build is a sub command provided by podman and docker that creates and starts containers from container images. Note that creating and starting containers are two separate operations, and run does both of them for you.
- `-name explorecalifornia.com` gives our container a name, like the -t option that we saw when we ran podman build, this flag is optional but highly recommended. Unlike build though, podman will give our container an easily identifiable name along with the longest ID that we can use to find the container later. 
- `--rm` tells podman to remove the container once the application its container image was configured to start finishes. This is a useful option that helps keep your container engine clean.
- ` -p 5000:80 ` tells podman to map port 5000 [**outside**] on your machine to port 80 [**inside**] of the container. This way we can access the application served through that port within the container, like any other process in our machine. 
  - It's easy to mix up which port is which. A helpful aid that I like to use to help me remember the two, is outside, colon, inside. Outside is the port outside of the container. Colon is, well, colon, and inside is the port inside of the container. This works on every container runtime, and even within Kubernetes via kubectl.
  - How did we know to map port 5000 on our machine to port 80? The EXPOSE command in our container file, that's how. While this isn't always a reliable source of documentation, it's usually a good place to start. 
- `**explorecalifonia.com**`  we provide the name of the container image we want to use at the end of the command, which in our case is the explorecalifornia.com image we built earlier.
{{% /details %}}


{{< image src="./r1.jpg" >}}
{{< image src="./r2.jpg" >}}

## **Challenge:** Changing the website's title


{{% hint info %}} 
- You've recently updated the index.htm file in the website directory to see how changing the website's title affects website traffic. Instead of it saying Welcome to Explore California, it now says Tours, Adventure, Travel, Explore California. 
- Additionally, your cybersecurity team asked you to configure Nginx so that it serves the Explore California website from /**usr/share/nginx/website**. You've also made these changes and want to make sure that everything works.
{{% /hint %}}

- **Challenge:**
  1. Create a new container with these changes **called explorecalifornia.com-new**.
  2. You should see "Tours, Adantures, Travel | Explorer Califonia" in the websites's title upon vistiting **localhost:5000**.

## **Solution:** Changing the website's title

- Edit file: **index.html**
  ```
  vim website/index.html
  ```

{{< image src="./c1.jpg" >}}
{{< image src="./c2.jpg" >}}

- Edit file: **nginx.conf**

{{< image src="./c3.jpg" >}}
{{< image src="./c4.jpg" >}}

- Edit dockerfile: **Containerfile**

{{< image src="./c6.jpg" >}}

- Build container:
  ```
  podman build -t explorecalifornia.com-new .
  ```
- Run container:
  ```
  podman run --rm --name explorecalifornia.com-new -p 5000:80 explorecalifornia.com-new
  ```
  {{< image src="./c7.jpg" >}}
  {{< image src="./c8.jpg" >}}
  {{< image src="./c9.jpg" >}}