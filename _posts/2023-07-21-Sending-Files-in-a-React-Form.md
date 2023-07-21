---
title: "Sending Files in a React Form"
date: 2023-07-21
---

In modern web applications, file uploads are a common requirement, allowing users to submit documents, images, and other files through forms. In this quick blog, we will explore how to create a simple file upload tool using React and demonstrate how to integrate it into a form.

## Prerequisites:

In a recent project, we had a specific requirement to enable users to upload Excel files in a React application. These files were intended to be processed on a server. In this article, we will focus on the crucial part of the code that utilizes Axios to send the uploaded file to the server.

Please note that this article solely aims to demonstrate the essential part of the code that sends the file via Axios, and additional details such as server-side processing may vary depending on your specific project requirements.

## The code

Here is the code for a very simple file upload component:

```tsx
import React, { useState, ChangeEvent } from "react";
import axios from "axios";

export default function App() {
  const [file, setFile] = useState<File | null>(null);

  const handleFileChange = (e: ChangeEvent<HTMLInputElement>) => {
    const selectedFile = e.target.files && e.target.files[0];
    setFile(selectedFile || null);
  };

  const handleUpload = async () => {
    if (file) {
      try {
        const formData = new FormData();
        formData.append("file", file);

        const response = await axios.post(
          "http://127.0.0.1:5000/upload",
          formData,
          {
            headers: {
              "Content-Type": "multipart/form-data"
            },
            responseType: "blob"
          }
        );
      } catch (error) {
        console.error(error);
      }
    }
  };

  return (
    <div>
      <h1>
        File Upload Tool
      </h1>
      <div>
        <label>
          File Upload:
        </label>
        <div>
          <label
            htmlFor="file-input"
          >
            {"Choose " + (file ? "different " : "") + "file"}
          </label>
          <input
            id="file-input"
            type="file"
            accept=".xlsx, .xls"
            onChange={handleFileChange}
          />
        </div>
        <p>
          Accepted file formats: .xlsx, .xls
        </p>
        {file && (
          <div>
            <label>
              Selected file:
            </label>
            <p>{file.name}</p>
          </div>
        )}
        <button
          onClick={handleUpload}
          disabled={!file}
        >
          "Upload"
        </button>
      </div>
    </div>
  );
}
```

## File Upload Input

The file upload input HTML tag is set to allow Excel files only in this case. It calls the `handleFileChange` method, which sets the `file` state variable.

```html
<input
  id="file-input"
  type="file"
  accept=".xlsx, .xls"
  onChange={handleFileChange}
/>
```

## Upload Process

Once a file is selected, the Upload button becomes enabled. When clicked, it initiates the `handleUpload` onClick method. This method utilizes Axios to send the file to the specified endpoint.

```tsx
const handleUpload = async () => {
    if (file) {
      try {
        const formData = new FormData();
        formData.append("file", file);

        const response = await axios.post(
          "http://127.0.0.1:5000/upload",
          formData,
          {
            headers: {
              "Content-Type": "multipart/form-data"
            },
            responseType: "blob"
          }
        );
      } catch (error) {
        console.error(error);
      }
    }
  };
```

## Conclusion

This was a quick tutorial on how to upload and send files using React and Axios. In a future article, I will describe how those files can be received and processed by the backend server.
