# **Event Management Automation**

## **Automating Workflow and SQL Database Setup on PythonAnywhere**

### **1. Setting Up the SQL Database**
- **Create a MySQL Database**: Log in to PythonAnywhere. Navigate to the “Databases” tab. Create a new MySQL database. Note the connection details.
- **Design Database Schema**:

    - **Clients Table**:
      ```
      CREATE TABLE Clients (
          id INT AUTO_INCREMENT PRIMARY KEY,
          name VARCHAR(255),
          contact_email VARCHAR(255),
          planner_name VARCHAR(255)
      );
      ```

    - **Events Table**:
      ```
      CREATE TABLE Events (
          id INT AUTO_INCREMENT PRIMARY KEY,
          client_id INT,
          event_date DATE,
          location VARCHAR(255),
          ensemble_type VARCHAR(100),
          balance_due DECIMAL(10, 2),
          payment_status VARCHAR(50),
          FOREIGN KEY (client_id) REFERENCES Clients(id)
      );
      ```

    - **Music Selections Table**:
      ```
      CREATE TABLE MusicSelections (
          id INT AUTO_INCREMENT PRIMARY KEY,
          event_id INT,
          seating_of_parents VARCHAR(255),
          wedding_party VARCHAR(255),
          bridal_entrance VARCHAR(255),
          recessional VARCHAR(255),
          optional_selection VARCHAR(255),
          FOREIGN KEY (event_id) REFERENCES Events(id)
      );
      ```

    - **Musicians Table**:
      ```
      CREATE TABLE Musicians (
          id INT AUTO_INCREMENT PRIMARY KEY,
          name VARCHAR(255),
          instrument VARCHAR(100),
          availability_status VARCHAR(50),
          event_id INT,
          payment_amount DECIMAL(10, 2),
          payment_status VARCHAR(50),
          FOREIGN KEY (event_id) REFERENCES Events(id)
      );
      ```

### **2. Data Entry Automation with Python**
- **Extract Data from Contracts**:
    ```python
    from docx import Document
    import pymysql

    # Connect to MySQL database
    connection = pymysql.connect(
        host='your-database-host',
        user='your-database-username',
        password='your-database-password',
        database='your-database-name'
    )
    cursor = connection.cursor()

    # Load contract document
    doc = Document('template 1.docx')
    data = {}  # Extract necessary data from doc into this dictionary

    # Example: Add a client
    cursor.execute('''
        INSERT INTO Clients (name, contact_email, planner_name)
        VALUES (%s, %s, %s)
    ''', (data['client_name'], data['client_email'], data['planner_name']))

    connection.commit()
    connection.close()
    ```

- **Automate Music Selections**:
    ```python
    cursor.execute('''
        INSERT INTO MusicSelections (event_id, seating_of_parents, wedding_party, bridal_entrance, recessional, optional_selection)
        VALUES (%s, %s, %s, %s, %s, %s)
    ''', (event_id, selection_1, selection_2, selection_3, selection_4, selection_5))
    ```

- **Musician Hiring and Status Updates**:
    ```python
    cursor.execute('''
        INSERT INTO Musicians (name, instrument, availability_status, event_id, payment_amount, payment_status)
        VALUES (%s, %s, %s, %s, %s, %s)
    ''', (musician_name, instrument, 'Pending', event_id, payment_amount, 'Unpaid'))
    ```

### **3. Setting Up Scheduled Tasks**
- **Automate Reminders**:
    - Go to the “Tasks” tab in PythonAnywhere.
    - Set up a new scheduled task that runs a Python script at specific intervals to send reminders or update statuses.

### **4. Automating Instagram Posting with Python Colab**
#### **Setup Google Drive Integration**:
- **Mount Google Drive**:
    ```python
    from google.colab import drive
    drive.mount('/content/drive')
    ```

- **Access Content Folder**:
    ```python
    import os
    content_folder = '/content/drive/My Drive/InstagramContent/'
    ```

#### **Generate Captions Using OpenAI API**:
- **Install OpenAI**:
    ```bash
    !pip install openai
    ```

- **Generate Captions**:
    ```python
    import openai

    openai.api_key = 'your-openai-api-key'

    def generate_caption(image_description):
        response = openai.Completion.create(
            engine="text-davinci-003",
            prompt=f"Generate an Instagram caption for: {image_description}",
            max_tokens=50
        )
        caption = response.choices[0].text.strip()
        return caption
    ```

#### **Schedule Instagram Posts Using Instagram Graph API**:
- **Install Requests**:
    ```bash
    !pip install requests
    ```

- **Post to Instagram**:
    ```python
    import requests

    access_token = 'your-instagram-access-token'
    instagram_account_id = 'your-instagram-account-id'

    def post_to_instagram(image_path, caption):
        media_endpoint = f"https://graph.facebook.com/v15.0/{instagram_account_id}/media"
        image_url = f"https://www.example.com/{image_path}"  # Upload your image and get a URL or use a direct link

        # Upload the image
        media_data = {
            'image_url': image_url,
            'caption': caption,
            'access_token': access_token
        }
        media_response = requests.post(media_endpoint, data=media_data)
        media_id = media_response.json().get('id')

        # Publish the post
        publish_endpoint = f"https://graph.facebook.com/v15.0/{instagram_account_id}/media_publish"
        publish_data = {
            'creation_id': media_id,
            'access_token': access_token
        }
        publish_response = requests.post(publish_endpoint, data=publish_data)
        return publish_response.json()
    ```

#### **Automate the Workflow**:
- **Automate Post Selection and Caption Generation**:
    ```python
    import random

    files = os.listdir(content_folder)
    random_file = random.choice(files)

    # Generate a caption based on file name or description
    caption = generate_caption(random_file)

    # Post to Instagram
    post_response = post_to_instagram(random_file, caption)
    print(post_response)
    ```

### **5. Building the Client and Musician Dashboard (Future Step)**
- **Set Up a Flask or Django Application**:
    - Build a backend using Flask or Django to handle user authentication and interactions with the SQL database.

- **Client Music Selection**:
    - Create forms for clients to select music for each event segment.
    - Update the `MusicSelections` table upon form submission.

- **Musician Access**:
    - Allow musicians to log in, view their upcoming events, and see their payment amounts.

- **Host the App on PythonAnywhere**:
    - Deploy the Flask/Django app on PythonAnywhere, making it accessible to clients and musicians.

### **6. Continuous Integration and Improvement**
- **Feedback and Iteration**:
    - Gather feedback from clients and musicians after initial deployment.
    - Regularly update your Python scripts, SQL database structure, and overall workflow based on the feedback received.
    - Implement new features or improve existing processes to streamline your operations further.

- **Monitoring and Testing**:
    - Periodically test all parts of your workflow to ensure they are functioning as expected.
    - Set up automated tests (if needed) to verify the integrity of your database and scripts.

- **Documentation**:
    - Keep your `README.md` and any other relevant documentation updated with the latest changes and instructions.
    - Document any new features or processes you implement so they can be easily understood and maintained.

### **7. Implementing Version Control with Git**
- **Initialize Git Repository**:
    ```bash
    git init
    ```

- **Add Files to the Repository**:
    ```bash
    git add .
    ```

- **Make the Initial Commit**:
    ```bash
    git commit -m "Initial commit"
    ```

- **Track Changes and Manage Versions**:
    - Use the following Git commands to manage your repository:
        - **Commit Changes:** Save changes to your repository with `git commit -m "Your message"`.
        - **View History:** Use `git log` to view the commit history.
        - **Create Branches:** Use `git branch branch_name` to create a new branch.
        - **Switch Branches:** Use `git checkout branch_name` to switch between branches.
        - **Merge Branches:** Use `git merge branch_name` to merge changes from one branch into another.
        - **Push to Remote Repository:** Use `git push origin branch_name` to push your changes to a remote repository like GitHub.
        - **Pull Updates:** Use `git pull origin branch_name` to pull updates from a remote repository.

- **Integrating Git with VS Code**:
    - Use the Git integration in VS Code to visualize changes, manage commits, and handle merges with a user-friendly interface.
    - Access Git features directly in VS Code using the Source Control view (`Ctrl + Shift + G`).

- **Set Up Remote Repository (Optional)**:
    - If you want to collaborate with others or back up your work:
        - Create a repository on GitHub or another Git hosting service.
        - Add the remote repository in your local Git setup:
            ```bash
            git remote add origin https://github.com/yourusername/your-repo-name.git
            ```
        - Push your initial commit to the remote repository:
            ```bash
            git push -u origin master
            ```

## **Current Development**
- Implementing automated task scheduling (in progress)
- Building client and musician dashboard using Flask
- Integrating Dropbox API for automated file management
- Automating Instagram posting using Python Colab, Google Drive, OpenAI, and Instagram Graph API
