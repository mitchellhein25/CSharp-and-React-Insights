---
title: "Processing Files in a Python Flask HTTP Server"
date: 2023-07-24
---

In a previous blog I discussed sending files in a React form: [Sending Files in a React Form](https://mitchellhein25.github.io/CSharp-and-React-Insights/2023/07/21/Sending-Files-in-a-React-Form.html). In that project, I was sending the file to a Python Flask server, so I also wanted to write a post about how that API endpoint works as well.

## The Code

```python
from datetime import datetime
from flask import Flask, request, send_file
from service.file_clean_service import FileCleanService
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

@app.route('/')
def home():
    return 'Home Page Route'

@app.route('/upload', methods=['POST'])
def upload_file():
    try:
        file = request.files['file']
        file_in_temp_mem = file.read()
        file_clean_service = FileCleanService(file=file_in_temp_mem)
        zip_result = file_clean_service.clean_and_package_in_zip()
        dt_now = datetime.now().strftime("%d%b%Y-%H%M%S")
        return send_file(zip_result, mimetype='application/zip', as_attachment=True, download_name=f'results_{dt_now}.zip')
    except KeyError:
        return 'No file uploaded.', 400

    except Exception as e:
        return f'An error occurred: {str(e)}', 500

if __name__ == '__main__':
    app.run()
```

## The Breakdown

### Reading the file

The following code gets the file from the request and then reads the contents and assigns it to a variable:

```python
file = request.files['file']
file_in_temp_mem = file.read()
```

### Processing the file

The following code is a custom service that performed some data transformation. It then returns a zip file result.

```python
file_clean_service = FileCleanService(file=file_in_temp_mem)
zip_result = file_clean_service.clean_and_package_in_zip()
```

### Returning a response

The following code uses the `send_file` method from the Flask package. It sends the zip file back in the response as an attachment.

```python
return send_file(zip_result, mimetype='application/zip', as_attachment=True, download_name=f'results_{dt_now}.zip')
```

## Conclusion

This article provided a quick example of how to receive and send files in a Flask server, specifically receiving an Excel file and sending back a zip.
