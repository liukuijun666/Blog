For clusters created using Easysearch Chart versions earlier than 0.2.0 (including 0.2.0), the initial password of the admin user is admin. Such clusters have security risks.

Easysearch 1.8.3 optimizes the initialization script (bin/initialize.sh) and adds the initialization password environment variable judgment. You can manually set the EASYSEARCH_INITIAL_ADMIN_PASSWORD environment variable to specify the initial password of admin user in the cluster.

Now let's adapt the Easysearch Chart package to this feature:

1. Download and unzip the Easysearch Chart package source code.

Download: https://github.com/infinilabs/helm-charts/releases

2. Modify the package file (Chart.yaml) to adjust the Easysearch version.

```plain
appVersion: 1.8.3-265
```

3. Modify the variables file (value.yaml) to add variables.

```plain
initAdminPassword: "admin111"
```

4. Modify the template file (templates/statefulset yaml), the initial container add environment variable configuration.

```plain
      initContainers:
        - name: init-config
          ......
          env:
            ......
            - name: EASYSEARCH_INITIAL_ADMIN_PASSWORD
              value: '{{- toString .Values.initAdminPassword }}'
```

5. Modify the template file (templates/configmap.yaml) and add the cluster initialization script (bin/initialize.sh) to the initial container startup script (unitMGR).

```plain
apiVersion: v1
kind: ConfigMap
......
data:
  unitMGR: |
    #!/bin/bash

    rm -rf /etc/confd/*
    ./bin/initialize.sh -s
    ......
```

Completed.

Verify:

![](https://infinilabs.cn/img/blog/2024/easysearch-chart-admin-password-custom/1.png)
