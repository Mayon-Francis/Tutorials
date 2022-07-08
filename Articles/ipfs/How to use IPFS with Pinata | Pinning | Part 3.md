In the previous article, we were looking into how ipfs actually works. We also downloaded and ran our own local ipfs node in the first article!! If you missed them, check them out here : < LINK >

Now, we’ll be looking into how we can use ipfs with a web app.

### Getting Started

In order to use our ipfs node and to interact with it from a client browser, we could run a dedicated server and an IPFS node inside it. 

OR… we can use a pinning service called [Pinata](https://www.pinata.cloud/). Pinata offers API endpoints for developers to interact with ipfs network without the hassle of setting up a dedicated backend with custom ipfs nodes.

So, let’s get started!!

### What we’ll be doing

We’ll build a website that allows users to upload images to the ipfs network.

### 1. ****Initialize our Project****

Create a folder and create an empty index.html file in it.

*CLI commands*

```bash
mkdir ipfs-quickStart && cd ipfs-quickStart
touch index.html
```

Now open up index.html in your favourite text editor and add the following boiler plate markup

```bash
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>IPFS with Pinata!!</title>
    <link rel="stylesheet" href="./style.css" />
  </head>
  <body>
    <main>
      <div class="container">
        <div class="card">
          <h3>Upload Files</h3>
          <div class="drop_box">
            <header>
              <h4>Select File here</h4>
            </header>
            <p>Files Supported: PNG, JPG, JPEG</p>
            <input type="file" hidden accept=".png,.jpg,.jpeg" id="fileID" style="display:none;">
            <button class="btn">Choose File</button>
          </div>
          <div class="ipfs-link">
          </div>
        </div>
      </div>
    </main>
    <script src="index.js"></script>
  </body>
</html>
```

**Explanation**

We have added a basic file upload option. We’ve also added an empty div which will contain the ipfs link we’ll get after uploading.

We’ll also add some basic stylings!!

create `style.css` in the same directory and add the following styles to it:

```bash
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@200;300;400;500;600;700&display=swap");
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans:ital,wght@0,400;0,700;1,400;1,700&display=swap');

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: "Poppins", sans-serif;
}

.container {
    height: 100vh;
    width: 100%;
    align-items: center;
    display: flex;
    justify-content: center;
    background-color: #fcfcfc;
}

.card {
    border-radius: 10px;
    box-shadow: 0 5px 10px 0 rgba(0, 0, 0, 0.3);
    width: 600px;
    height: 260px;
    background-color: #ffffff;
    padding: 10px 30px 40px;
}

.card h3 {
    font-size: 22px;
    font-weight: 600;

}

.drop_box {
    margin: 10px 0;
    padding: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    border: 3px dotted #a3a3a3;
    border-radius: 5px;
}

.drop_box h4 {
    font-size: 16px;
    font-weight: 400;
    color: #2e2e2e;
}

.drop_box p {
    margin-top: 10px;
    margin-bottom: 20px;
    font-size: 12px;
    color: #a3a3a3;
}

.btn {
    text-decoration: none;
    background-color: #005af0;
    color: #ffffff;
    padding: 10px 20px;
    border: none;
    outline: none;
    transition: 0.3s;
}

.btn:hover {
    text-decoration: none;
    background-color: #ffffff;
    color: #005af0;
    padding: 10px 20px;
    border: none;
    outline: 1px solid #010101;
}

.form input {
    margin: 10px 0;
    width: 100%;
    background-color: #e2e2e2;
    border: none;
    outline: none;
    padding: 12px 20px;
    border-radius: 4px;
}
```

Open up index.html in your browser and you should see something like this!

![image](https://user-images.githubusercontent.com/75270610/178026633-ae5e18c8-2e73-4e19-978f-c71a774c1b43.png)

### 2. Setting up Pinata

To get started with pinata, first go to the [pinata website](https://www.pinata.cloud/) and sign up.

Next go to the [developer portal](https://app.pinata.cloud/keys).

Create a new API KEY using the “+” icon:

![image](https://user-images.githubusercontent.com/75270610/178026692-4b4af2ec-6249-43a7-bb5e-ed802cd7802c.png)

![image](https://user-images.githubusercontent.com/75270610/178026752-4a17cbb2-3bf2-4b54-bbd8-a6401e28f419.png)

You can check all pinning options, or enable all options by checking the “admin” switch.

Give your key a name and press create!

![image](https://user-images.githubusercontent.com/75270610/178026813-ae7f9aae-60ba-496e-bf29-1a369d873a4b.png)

**Important: Copy and securely store your Api key, secret and JWT Key, We’ll be using them to access pinata endpoints.**

### 3. Adding Javascript!

Create an `index.js` file in the same directory and add the following basic page manipulation code:

```bash
const dropArea = document.querySelector(".drop_box"),
  button = dropArea.querySelector("button"),
  dragText = dropArea.querySelector("header"),
  input = dropArea.querySelector("input");

const ipfsLink = document.querySelector(".ipfs-link");
let file;
var fileName;

button.onclick = () => {
  input.click();
};

input.addEventListener("change", function (e) {
  fileName = e.target.files[0].name;
  file = e.target.files[0];
  let filedata = `
    <div class="form">
    <h4>${fileName}</h4>
    <button class="btn">Upload</button>
    </div>`;
  dropArea.innerHTML = filedata;
  const uploadBtn = document.querySelector(".btn");
  uploadBtn.onclick = uploadFile;
});
```

We’re just looking for a file upload and then replacing the upload area with the file data and an upload button. the `uploadFile` function will be where the magic of uploading to pinata happens.

**Upload File**

The endpoint to upload file to pinata is `https://api.pinata.cloud/pinning/pinFileToIPFS`

To upload a file here, we can attach the file using the [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) interface.

We’ll first create a new FormData and include the fileName for pinata metadata, and attach the file as well.

```bash
const data = new FormData();
    const metadata = JSON.stringify({
        name: fileName
    });
    data.append('pinataMetadata', metadata);
    data.append('file', file, file.name);
```

Next,  add your pinataJWT (that we got in the previous step). To use the endpoint, we’ll attach it as a bearer token.

```jsx
let PinataJWT = 'REPLACE_THIS_WITH_THE_JWT_YOU_GOT_FROM_PINATA';
 const res = await fetch(`https://api.pinata.cloud/pinning/pinFileToIPFS`,{
      method: 'POST',
      headers: {
          Authorization: `Bearer ${PinataJWT}`,
      },
      body: data
  });
let fileHash = (await res.json()).IpfsHash;
```

**That was easy!!** The response contains the ipfs hash of the file we uploaded

the response is of the form

```jsx
{
    "IpfsHash": "QmbXs74M9bL9uTttKVnRDHojoUCLdnLu5rS5AXGrWorA4E",
    "PinSize": 3195,
    "Timestamp": "2022-07-07T15:33:49.440Z",
    "isDuplicate": true
}
```

Ipfs files can be accessed over any public gateway.

we can use the `ipfs.io` gateway. it uses the syntax  `https://ipfs.io/ipfs/<fileHash>`

Add  the follwing snippet to include the url as well

```jsx
let fileHash = (await res.json()).IpfsHash;
let fileUrl = `https://ipfs.io/ipfs/${fileHash}`
ipfsLink.innerHTML = `
<p>Your file was uploaded to <a target='_blank' href=${fileUrl}>Link to file!</a></p>
`;
```

The complete code looks like this:

```jsx
let PinataJWT = 'YOUR_PINATA_JWT';

const dropArea = document.querySelector(".drop_box"),
  button = dropArea.querySelector("button"),
  dragText = dropArea.querySelector("header"),
  input = dropArea.querySelector("input");

const ipfsLink = document.querySelector(".ipfs-link");
let file;
var fileName;

button.onclick = () => {
  input.click();
};

input.addEventListener("change", function (e) {
  fileName = e.target.files[0].name;
  file = e.target.files[0];
  let filedata = `
    <div class="form">
    <h4>${fileName}</h4>
    <button class="btn">Upload</button>
    </div>`;
  dropArea.innerHTML = filedata;
  const uploadBtn = document.querySelector(".btn");
  uploadBtn.onclick = uploadFile;
});

async function uploadFile() {
    const data = new FormData();
    const metadata = JSON.stringify({
        name: fileName
    });
    data.append('pinataMetadata', metadata);
    data.append('file', file, file.name);
    const res = await fetch(`https://api.pinata.cloud/pinning/pinFileToIPFS`,{
        method: 'POST',
        headers: {
            Authorization: `Bearer ${PinataJWT}`,
        },
        body: data
    });

    let fileHash = (await res.json()).IpfsHash;
    let fileUrl = `https://ipfs.io/ipfs/${fileHash}`
    ipfsLink.innerHTML = `
    <p>Your file was uploaded to <a target='_blank' href=${fileUrl}>Link to file!</a></p>
    `;

}
```

The output looks like this:

![image](https://user-images.githubusercontent.com/75270610/178026905-a245802c-094b-4d5e-bebf-13554454291b.png)


[Link to Demo File](https://ipfs.io/ipfs/QmbXs74M9bL9uTttKVnRDHojoUCLdnLu5rS5AXGrWorA4E)

### Yay! we’ve successfully uploaded files to IPFS with the help of pinata.

All the code that we just ran through is over here 👉 [Github repository](https://github.com/UV-Labs/Tutorials). Do give us a star, clap if you liked our work.

Authors (open to feedback): 👇

Mayon Francis , Vijay Krishnavanshi and Amateur Dev
