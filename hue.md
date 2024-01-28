# hue

## forget admin account password

ref to [](https://gethue.com/password-management-in-hue/)

1.  login into hue server as root
2.  set HUE_CONF_DIR

    export HUE_CONF_DIR="/var/run/cloudera-scm-agent/process/<ID>-hue-HUE_SERVER/"

3.  find build/env/bin/hue and use shell

    sudo /opt/cloudera/parcels/&lt;CDN_VERSION>/lib/hue/build/env/bin/hue shell --cm-managed

4.  set password

```python
from django.contrib.auth.models import User
user = User.objects.get(username='admin')
user.set_password('<new_password>')
user.save()
```
