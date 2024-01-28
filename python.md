# python

## cProfile

```python
import cProfile, pstats, io
from pstats import SortKey
pr = cProfile.Profile()
pr.enable()
# insert function executing
pr.disable()
s = io.StringIO()
ps = pstats.Stats(pr, stream=s).sort_stats(SortKey.CUMULATIVE)
ps.print_stats(10)
print(s.getvalue())
```
## logging
```python
from importlib import reload
import logging
reload(logging)

logger = logging.getLogger('my_logger')
logging.basicConfig(filename="/path/to/log/file",
                    level=logging.DEBUG,
                    format='%(asctime)s : %(levelname)s - %(message)s')
```

## http server

1. open a http server to receive file

```python
import os
try:
    import http.server as server
except ImportError:
    # Handle Python 2.x
    import SimpleHTTPServer as server

class HTTPRequestHandler(server.SimpleHTTPRequestHandler):
    """Extend SimpleHTTPRequestHandler to handle PUT requests"""
    def do_PUT(self):
        """Save a file following a HTTP PUT request"""
        filename = os.path.basename(self.path)

        # Don't overwrite files
        if os.path.exists(filename):
            self.send_response(409, 'Conflict')
            self.end_headers()
            reply_body = '"%s" already exists\n' % filename
            self.wfile.write(reply_body.encode('utf-8'))
            return

        file_length = int(self.headers['Content-Length'])
        with open(filename, 'wb') as output_file:
            output_file.write(self.rfile.read(file_length))
        self.send_response(201, 'Created')
        self.end_headers()
        reply_body = 'Saved "%s"\n' % filename
        self.wfile.write(reply_body.encode('utf-8'))

if __name__ == '__main__':
    server.test(HandlerClass=HTTPRequestHandler)
```

2. send file

```bash
curl -X PUT --upload-file my_file.txt http://<ip>:8000
```

### open server

```bash
# python2
python -m SimpleHTTPServer 

# python3
python -m http.server
```

### delete python3.10 in MacOS

```bash
sudo rm -rf /Library/Frameworks/Python.framework/Versions/3.10
sudo rm -rf “/Applications/Python 3.10”
ls -l /usr/local/bin | grep ‘../Library/Frameworks/Python.framework/Versions/3.10’
# check $PATH
```
