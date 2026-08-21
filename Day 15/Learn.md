## VPS

VPS = Virtual Private Server

A VPS is a virtual computer/server that you rent from a hosting provider.

used to host websites, web apps, APIs, databases, etc.

VPS → virtual server running inside a physical server

Physical server:  
→ powerful computer in a data center

VPS:  
→ virtual machine created from that physical server

Example:

```
Physical Server
├── VPS 1
├── VPS 2
├── VPS 3
└── VPS 4
```

Each VPS has its own:  
→ CPU  
→ RAM  
→ storage  
→ operating system  
→ IP address

usually runs Linux such as Ubuntu

You can access a VPS remotely using SSH:

```
ssh root@SERVER_IP
```

Example:

```
ssh root@123.45.67.89
```

after connecting:

```
your computer
    ↓
  Internet
    ↓
   VPS
    ↓
 Ubuntu Linux
```

VPS is basically → a remote Linux computer that you control

## VPS vs Physical Server

Physical server:  
→ actual computer hardware  
→ you own/manage the hardware  
→ more control  
→ higher maintenance

VPS:  
→ virtual server  
→ physical hardware is owned by hosting provider  
→ you rent the virtual server  
→ easier and cheaper

## VPS vs Hosting

Hosting = making your website/application available on the internet

VPS = one way to provide the server used for hosting

Example:

```
VPS
 ↓
Ubuntu
 ↓
Nginx
 ↓
Your application
 ↓
Website
```

## Domain + VPS

Domain → human-readable website name

VPS → server where your application runs

Example:

```
example.com
     ↓
    DNS
     ↓
VPS IP address
     ↓
   Nginx
     ↓
Application
```

## Common VPS components

IP address  
→ identifies the VPS on the network

SSH  
→ used to remotely access the VPS

Ubuntu  
→ operating system running on the VPS

Nginx  
→ web server / reverse proxy

Firewall  
→ controls incoming and outgoing network traffic

Application  
→ your website/API/backend

Database  
→ stores application data

## Basic deployment flow

```
Buy VPS
 ↓
Choose Linux OS
 ↓
Get VPS IP
 ↓
Connect using SSH
 ↓
Install required software
 ↓
Upload/clone project
 ↓
Run application
 ↓
Configure Nginx
 ↓
Point domain to VPS
 ↓
Enable HTTPS
 ↓
Website is live
```

## Important terms

VPS → virtual computer you rent

Server → computer that provides services

Hosting → putting your application on a server so others can access it

Domain → name used to access your website

DNS → converts domain name → IP address

IP address → identifies the server

SSH → remotely access the server

Nginx → web server / reverse proxy

HTTPS → encrypted HTTP connection

Firewall → controls network access

Self-hosting → hosting the application on a server you own

VPS hosting → hosting the application on a rented VPS