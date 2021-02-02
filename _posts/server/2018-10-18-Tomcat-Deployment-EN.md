---
toc: true
title: "Possible Tomcat App Deployments"
description: "By Manager App, By webapps dir, By context.xml"
categories: [Server]
tags: [Tomcat]
redirect_from:
  - /2018/10/18/
---

> In this page, I will show you three different way to deploy applications

### By Manager App

```yaml
visit tomcat base url
localhost:8080
Click "Manager App"
You can deploy by specifying directory path or war file
```

### By webapps dir

```yaml
You can copy app files into webapps
If you look inside of conf/server.xml webapps attributes, you can see that tomcat automatically deploy application within webapps dir
```

### By context.xml

```yaml
Inside of conf/Catalina/localhost folder, create context.xml file and configue your app deployment.
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io