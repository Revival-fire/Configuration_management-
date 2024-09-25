# Critical Thinking Automated Deployment of Ansible Playbooks using GitHub Actions


This project provides an excellent opportunity to combine Ansible and GitHub Actions to automate AWS infrastructure deployment. Here's a detailed execution plan for each of your goals:

### 1. **Repository Setup**
   - **Create GitHub Repository**: 
     - Name it something like `aws-ansible-deployment`.
     - Initialize the repository with a `README.md` explaining the project scope and setup.
     - Organize folders for playbooks, roles, and any other infrastructure code.

### 2. **GitHub Actions Configuration**
   - **GitHub Actions Workflow Setup**: 
     - Create a folder `.github/workflows` and add a file `deploy.yml`.
     - The workflow file should be triggered by push events to a specific branch (e.g., `main`).
     - Store AWS credentials (Access Key ID, Secret Access Key) securely in **GitHub Secrets**:
       - Go to **Settings > Secrets** in your repository.
       - Add `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.
     - Here’s a sample `deploy.yml` to kickstart the workflow:
     ```yaml
     name: Deploy Infrastructure

     on:
       push:
         branches:
           - main

     jobs:
       deploy:
         runs-on: ubuntu-latest

         steps:
           - name: Checkout code
             uses: actions/checkout@v2

           - name: Set up Python
             uses: actions/setup-python@v2
             with:
               python-version: '3.x'

           - name: Install Ansible
             run: |
               python3 -m pip install --upgrade pip
               pip install ansible boto3

           - name: Run Ansible Playbook
             env:
               AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
               AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
             run: |
               ansible-playbook -i inventory playbooks/site.yml
     ```

### 3. **Ansible Playbook Development**
   - **Ansible Playbooks for AWS**:
     - Use Ansible roles to organize tasks related to EC2, EFS, MySQL, and Apache:
       - **EC2 provisioning**: Using `ec2` Ansible module.
       - **EFS Creation**: Using the `efs` module.
       - **MySQL and Apache setup**: Using Ansible's `yum`, `apt`, and `service` modules.
       - **EFS Mounting**: Configure EC2 instances to automatically mount the EFS file system using the `mount` module.
     - **Example Structure**:
     ```
     .
     ├── playbooks/
     │   └── site.yml
     └── roles/
         ├── ec2/
         ├── efs/
         ├── mysql/
         └── apache/
     ```
     - **Example Playbook (site.yml)**:
     ```yaml
     ---
     - hosts: localhost
       gather_facts: no
       roles:
         - ec2
         - efs
         - mysql
         - apache
     ```
     - Ensure to include variable files, templates, and configurations for flexibility.

### 4. **GitHub Actions Workflow Execution**
   - **Execution Details**:
     - The GitHub Actions workflow (`deploy.yml`) will:
       1. Check out the repository code.
       2. Install Ansible and AWS SDK for Python (`boto3`).
       3. Authenticate using the credentials stored in GitHub Secrets.
       4. Run the playbook with `ansible-playbook` to provision and configure the infrastructure.

### 5. **Testing and Validation**
   - **Push to GitHub**:
     - Test the automation by pushing changes to the repository. Ensure the workflow gets triggered, and verify AWS resources are provisioned as expected.
     - **Validation**:
       - Log into the AWS console to confirm EC2 instances are created, EFS is mounted, and MySQL and Apache servers are correctly installed.
       - SSH into the EC2 instances to verify the MySQL and Apache configurations, and check if EFS is mounted.

### 6. **Error Handling and Notifications**
   - **Add Error Handling**: 
     - In the workflow file, include steps to capture errors and send notifications using tools like Slack or email.
     ```yaml
     - name: Notify on Failure
       if: failure()
       run: |
         echo "Deployment failed!" | mail -s "Deploy Failed" your-email@example.com
     ```
   - **Using Ansible Error Handling**: 
     - Add `ignore_errors: false` to critical tasks and use handlers to respond to failures within the playbooks.

### 7. **Documentation and Maintenance**
   - **README.md** should document the following:
     - How to configure GitHub Secrets.
     - The structure of the playbooks and roles.
     - Instructions on how to trigger workflows.
     - How to monitor workflows and handle failures.

By implementing this project, I gained hands-on experience with **GitHub Actions, Ansible, AWS automation**, and **infrastructure-as-code (IaC)** workflows, helping you understand real-world CI/CD automation.
