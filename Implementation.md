### 1. 🛠 Create an IAM User in AWS

- Go to [AWS IAM Console](https://console.aws.amazon.com/iam/)
- Create a user with **AWS S3 Full Access** 
- Attach the following policy
- <img width="1887" height="730" alt="image" src="https://github.com/user-attachments/assets/44b53084-7c4c-4577-af2d-61d4ccb4230b" />
<img width="1900" height="771" alt="image" src="https://github.com/user-attachments/assets/68a7f8ae-2aae-4758-8c8e-480f3442c595" />




### ✅ 2. Create an S3 Bucket

- Go to the [AWS S3 Console](https://s3.console.aws.amazon.com)
- Click **Create bucket**
- Provide:
    - **Bucket name**: `your-unique-bucket-name`
    - **Region**: e.g., `Asia Pacific (Mumbai) - ap-south-1`
- Leave other settings as default and click **Create bucket**
  <img width="1881" height="824" alt="image" src="https://github.com/user-attachments/assets/0919add0-b267-4e6f-9828-68aa8705c808" />




### 🌐 3. Enable Static Website Hosting

- Go to your created bucket
- Click on the **Properties** tab
- Scroll to **Static website hosting** > Click **Edit**
- Enable `Static website hosting`
- Set:
   - **Index document**: `index.html`
   - **Error document**: `index.html` *(or `error.html` optionally)*
- Click **Save changes**

<img width="1883" height="833" alt="image" src="https://github.com/user-attachments/assets/f38ee154-0a3a-40e1-b753-bc228f5242fc" />


### 🔓 4. Allow Public Access

- In S3 bucket, Go to the **Permissions** tab
- Under **Block public access (bucket settings)**, click **Edit**
- **Uncheck all** options (especially "Block all public access")
- Confirm by checking the warning box
- Click **Save changes**

<img width="1885" height="767" alt="image" src="https://github.com/user-attachments/assets/2a7af0e2-daeb-41c9-a43b-15b04a6fe4e0" />



### 👥 5. Set Object Ownership to Public

- Still under the **Permissions** tab
- Scroll to **Object Ownership** and click **Edit**
- Choose:
   - `ACLs enabled`
   - `Bucket owner preferred`
- Save the changes

<img width="1889" height="824" alt="image" src="https://github.com/user-attachments/assets/9f5b2c72-7768-46eb-bb99-61b106fca302" />


### 🛡 6. Attach Public Read Policy Using Policy Generator

#### 🧰 Use AWS Policy Generator:

- Open: [AWS Policy Generator](https://awspolicygen.s3.amazonaws.com/policygen.html)

**Fill in:**
- **Effect**: `Allow`
- **Principal**: *
- **Action**: `s3:GetObject` or check All action
- **ARN**:  paste-your-S3-Bucket-ARN

<img width="1884" height="860" alt="image" src="https://github.com/user-attachments/assets/981500e0-f9ea-41eb-9e90-99b33c0da988" />
<img width="1901" height="775" alt="image" src="https://github.com/user-attachments/assets/448fa477-863e-471c-8c40-89488e75c85f" />



### 7. Create access-keys for IAM user

<img width="1901" height="817" alt="image" src="https://github.com/user-attachments/assets/c5d78552-b353-44c1-bca4-5b026a61e9c9" />


### 8. Go to GitHub Actions

- Open your GitHub repository.
- Click on the **"Actions"** tab.
- Click on **"Set up a workflow yourself"** or choose **"New workflow"**.


### 9. Create a Workflow File

- In the `.github/workflows/` folder, create a file named:  main.yml
- paste below code in the code area and click commit changes.

```bash
name: production testing pipeline

on:
  push:
    branches: [main]

jobs:
  deploy:
    name: Build, Test & Deploy Portfolio
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Validate Files Exist
      run: |
        test -f Devops-CI-CD/Test_Code/index.html || (echo "index.html missing" && exit 1)
        test -f Devops-CI-CD/Test_Code/style.css || (echo "style.css missing" && exit 1)
        test -f Devops-CI-CD/Test_Code/script.js || (echo "script.js missing" && exit 1)

    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: 'ap-south-1'

    - name: Deploy to S3
      run: |
        aws s3 sync Devops-CI-CD/Test_Code/ s3://${{ secrets.AWS_S3_BUCKET_NAME }} --delete

```
 
### 10. Add New repository secrets

- Go to settings > secrets and variables
- click add new repository secret.
- click actions.


<img width="1879" height="869" alt="image" src="https://github.com/user-attachments/assets/17b48bb5-b191-459b-8641-72c7f5956d06" />



<img width="1913" height="866" alt="image" src="https://github.com/user-attachments/assets/034f192d-6a3b-4c15-994e-8847c8537f43" />


#### This will create a CI/CD pipeline from our github repository to the AWS S3 bucket via the IAM user with a desired set of security policies.
