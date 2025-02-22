<!-- wp:paragraph -->
<p>Author: Chrisio (Weixi Guan) Gwaan<br>Date: 2024-09-12<br><strong>Note: The following installation guide is based on the Ubuntu system.</strong></p>
<!-- /wp:paragraph -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Step 1: GitHub Personal Access Token (PAT)</h2>
<!-- /wp:heading -->

<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li>Go to <code>GitHub profile</code> -&gt; <code>Settings</code> -&gt; <code>Developer settings</code> -&gt; <code>Personal Access Token</code>.</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>Generate a token with checking the following permission.</li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:image {"id":112,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/Screenshot-2024-09-11-130059.png" alt="" class="wp-image-112"/></figure>
<!-- /wp:image -->

<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li>Copy the token and keep it in the note. (Token will only be shown at the first time. If token is lost, you can generate a new one)</li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Step 2: Install Docker</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>1. Set up Docker's&nbsp;<code>apt</code>&nbsp;repository.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code># Add Docker's official GPG key:
sudo apt-get update

sudo apt-get install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb &#91;arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release &amp;&amp; echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list &gt; /dev/null

sudo apt-get update</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>2. Install the Docker packages.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code>sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>3. Check docker container list for verifying if docker is installed successfully.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code>sudo docker ps</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>4. Enable non-root user to use docker command without <code>sudo</code>.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code># Create the docker group if it does not exist
sudo groupadd docker

# Add your user to the docker group
sudo usermod -aG docker $USER

# Log in to the new docker group (to avoid having to log out / log in again; but if not enough, try to reboot)
newgrp docker

# Check if docker can be run without root
docker ps

# (Optional!) Reboot if still got error
reboot</code></pre>
<!-- /wp:code -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Step 3: Install Jenkins and Setup</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>1. Download and install a Long-Term Support (LTS) release Jenkins. (A LTS&nbsp;is chosen every 12 weeks from the stream of regular releases as the stable release for that time period)</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code>sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https:&#47;&#47;pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb &#91;signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list &gt; /dev/null

sudo apt-get update

sudo apt-get install jenkins</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>2. Installation of Java. (Jenkins requires Java to run)</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code>sudo apt update

sudo apt install openjdk-21-jdk

# Check if Java is installed successfully
java -version</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>3. Start Jenkins.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code># Enable the Jenkins service to start at boot
sudo systemctl enable jenkins

# Start the Jenkins service                                 
sudo systemctl start jenkins

   #if getting error: "System has not been booted with systemd as init system (PID 1). Can't operate.
   #Failed to connect to bus: Host is down"
   #try: sudo service jenkins start  expected output:  "* Starting Jenkins Automation Server jenkins  ..." -Dylan

# Check the status of the Jenkins service
# You should see a generated password for admin, similar example as shown below
sudo systemctl status jenkins

   #if getting error: "System has not been booted with systemd as init system (PID 1). Can't operate.
   #Failed to connect to bus: Host is down"
   #try: sudo service jenkins status expected output: " * jenkins is running" -Dylan

   #to get password (what is provided in the red box in the image below) 
   #try: sudo tail -f /var/log/jenkins/jenkins.log expected output: "{numbers &amp; letters AKA PASSWORD} This may also be ..."

   #to open it you can do http://localhost:8080

</code></pre>
<!-- /wp:code -->

<!-- wp:image {"id":121,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-1-1024x232.png" alt="" class="wp-image-121"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>As default, Jenkins will create an admin account that has all the permissions for the platform. The login username would be <code>admin</code>, and password would be the one with red colour highlight from the terminal. Remember to save this password in your note.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>4. Add port 8080 to inbound rule of the EC2 instance in AWS</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Click the following highlights:</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":122,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-2-1024x339.png" alt="" class="wp-image-122"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":123,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-3-1024x384.png" alt="" class="wp-image-123"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":125,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-4-1024x278.png" alt="" class="wp-image-125"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":127,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-5-1024x330.png" alt="" class="wp-image-127"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>And click <code>Save rules</code>.<br>After the TCP port 8080 is enabled at inbound rule, Jenkins can be accessed publicly from now on.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>5. Access Jenkins on browser.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Jenkins uses port 8080 as default, you may access it with the following URL:</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>http:&#47;&#47;&lt;replace_with_your_domain_here&gt;:8080</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>6. Unlock Jenkins with initial admin password.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":128,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-6-1024x437.png" alt="" class="wp-image-128"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>7. Install suggested plugins.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":129,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-7-1024x490.png" alt="" class="wp-image-129"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":130,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-8-1024x976.png" alt="" class="wp-image-130"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>8. (Optional) Create first admin user</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>You can skip and create later.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":132,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-9-1024x973.png" alt="" class="wp-image-132"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>9. Instance Configuration<br>You can just leave it as default and click to the next.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":136,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-10-1024x973.png" alt="" class="wp-image-136"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>10. Awesome! Jenkins is ready to use.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":137,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-11-1024x986.png" alt="" class="wp-image-137"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":138,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-12-1024x491.png" alt="" class="wp-image-138"/></figure>
<!-- /wp:image -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Step 4: Add GitHub PAT to Jenkins</h2>
<!-- /wp:heading -->

<!-- wp:image {"id":140,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-13-1024x491.png" alt="" class="wp-image-140"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":142,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-14-1024x490.png" alt="" class="wp-image-142"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":143,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-15-1024x490.png" alt="" class="wp-image-143"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":144,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/image-16.png" alt="" class="wp-image-144"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Green highlight section can be any name you want. Click the red highlight button to create a credential with a GitHub user's PAT.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":145,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-17-1024x396.png" alt="" class="wp-image-145"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":146,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-18-1024x533.png" alt="" class="wp-image-146"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Now you can put a user's PAT to the pink highlight section. You can also give the name of this PAT to the green highlight section.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":147,"width":"622px","height":"auto","sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large is-resized"><img src="https://hinna.io/wp-content/uploads/2024/09/image-19-1024x497.png" alt="" class="wp-image-147" style="width:622px;height:auto"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>After a credential is created, you can see an option from <code>credentials</code> section.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":148,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-20-1024x366.png" alt="" class="wp-image-148"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>To check if the PAT is working, simply just click the red highlight button, and you will see a status message with the GitHub userID of the PAT you provide from the green highlight section.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":150,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-22-1024x368.png" alt="" class="wp-image-150"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Now, click <code>save</code>.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":151,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-23-1024x492.png" alt="" class="wp-image-151"/></figure>
<!-- /wp:image -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Step 5: Generate SSH key</h2>
<!-- /wp:heading -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Connect to your EC2 instance.</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>Generate an ED25519 ssh key on your instance terminal, and replace the <code>your_email@example.com</code> with the one that belongs to the GitHub user who generated the PAT from the previous step.</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:code -->
<pre class="wp-block-code"><code>ssh-keygen -t ed25519 -C "your_email@example.com"
# Keep clicking 'Enter' as choosing default</code></pre>
<!-- /wp:code -->

<!-- wp:image {"id":154,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/image-24.png" alt="" class="wp-image-154"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>3. Print out the <code>private key</code>, and copy it.</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>cat ~/.ssh/id_ed25519</code></pre>
<!-- /wp:code -->

<!-- wp:image {"id":155,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/image-25.png" alt="" class="wp-image-155"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>4. Go to <code>Jenkins</code> -&gt; <code>Manage Jenkins</code> -&gt; <code>Security</code> -&gt; <code>Credentials</code></p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":157,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-27-1024x265.png" alt="" class="wp-image-157"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":158,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-28-1024x149.png" alt="" class="wp-image-158"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":159,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-29-1024x164.png" alt="" class="wp-image-159"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":160,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-30-1024x439.png" alt="" class="wp-image-160"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Paste the <code>private key</code> as shown below.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":161,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-31-1024x546.png" alt="" class="wp-image-161"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>5. Go back to the instance terminal, and print out the <code>public key</code> from the SSH key, then copy the entire <code>public key</code>.</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>cat ~/.ssh/id_ed25519.pub</code></pre>
<!-- /wp:code -->

<!-- wp:image {"id":162,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-32-1024x47.png" alt="" class="wp-image-162"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>6. Go to <code>GitHub profile</code> -&gt; <code>Settings</code> -&gt; <code>SSH and GPG keys</code> -&gt; Click <code>New SSH key</code> -&gt; Paste the <code>public key</code> to the <code>key</code> section.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":163,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-33-1024x531.png" alt="" class="wp-image-163"/></figure>
<!-- /wp:image -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Step 6: Grant Jenkins access to SSH key</h2>
<!-- /wp:heading -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Create the <code>.ssh</code> directory in the Jenkins folder.</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code># If the .ssh directory does not exist, create it and set the proper permissions
sudo mkdir /var/lib/jenkins/.ssh
sudo chown jenkins:jenkins /var/lib/jenkins/.ssh
sudo chmod 700 /var/lib/jenkins/.ssh</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>2. Copy the SSH Key Files.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code>sudo cp ~/.ssh/id_ed25519 /var/lib/jenkins/.ssh/
sudo cp ~/.ssh/id_ed25519.pub /var/lib/jenkins/.ssh/</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>3. Set Proper Permissions.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code># Make sure the Jenkins user owns the SSH key files and that permissions are set correctly
sudo chown jenkins:jenkins /var/lib/jenkins/.ssh/id_ed25519 /var/lib/jenkins/.ssh/id_ed25519.pub
sudo chmod 600 /var/lib/jenkins/.ssh/id_ed25519
sudo chmod 644 /var/lib/jenkins/.ssh/id_ed25519.pub</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>4. Switch to the Jenkins User.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code>sudo -u jenkins -s</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>5. Add GitHub’s Host Key.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code># Use ssh-keyscan to fetch GitHub's public key and add it to the known_hosts file
ssh-keyscan github.com &gt;&gt; ~/.ssh/known_hosts

# This command appends the GitHub host key to the Jenkins user’s known_hosts file.</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>6. Exit the Jenkins User Shell.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code>exit</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>7. Test the SSH Connection.</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code>sudo -u jenkins ssh -T git@github.com</code></pre>
<!-- /wp:code -->

<!-- wp:image {"id":168,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/image-34.png" alt="" class="wp-image-168"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>8. (Optional) Restart Jenkins</p>
<!-- /wp:paragraph -->

<!-- wp:code {"align":"wide"} -->
<pre class="wp-block-code alignwide"><code>sudo systemctl restart jenkins</code></pre>
<!-- /wp:code -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Step 7: Grant Jenkins access to use Docker command</h2>
<!-- /wp:heading -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Add Jenkins User to Docker Group.</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:code -->
<pre class="wp-block-code"><code>sudo usermod -aG docker jenkins</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>2. Verify Group Membership.</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>groups jenkins</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>3. Restart Jenkins.</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>sudo systemctl restart jenkins</code></pre>
<!-- /wp:code -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Step 8: Create a job for a project on Jenkins</h2>
<!-- /wp:heading -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Create a job.</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:image {"id":171,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-35-1024x490.png" alt="" class="wp-image-171"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":172,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-36-1024x489.png" alt="" class="wp-image-172"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>2. Go to <code>Source Code Management</code>.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":173,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-37-1024x488.png" alt="" class="wp-image-173"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>3. Go to your GitHub repository and copy the SSH link of the repo.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":174,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-38-1024x382.png" alt="" class="wp-image-174"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>4. Go back to <code>Jenkins</code> <code>Source Code Management</code> and paste the link to <code>Repository URL</code>. Then choose the credential that is created previously.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":175,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-39-1024x469.png" alt="" class="wp-image-175"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>5. Change the branch to the specific one you want for deployment.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":176,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-40-1024x469.png" alt="" class="wp-image-176"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>6. Enable GitHub hook trigger for GITScm polling.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>When Jenkins receives a GitHub push hook, GitHub Plugin checks to see whether the hook came from a GitHub repository which matches the Git repository defined in SCM/Git section of this job. If they match and this option is enabled, GitHub Plugin triggers a one-time polling on GITScm. When GITScm polls GitHub, it finds that there is a change and initiates a build. The last sentence describes the behavior of Git plugin, thus the polling and initiating the build is not a part of GitHub plugin.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":177,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/image-41.png" alt="" class="wp-image-177"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>7. Set commit status to send it to GitHub.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":178,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-42-1024x488.png" alt="" class="wp-image-178"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":179,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/image-43.png" alt="" class="wp-image-179"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Keep the rest of settings the same, just change to the one with red colour highlight.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":180,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-44-1024x486.png" alt="" class="wp-image-180"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>7. Go to <code>GitHub Repository</code> -&gt; <code>Settings</code> -&gt; <code>Webhooks</code> -&gt; <code>Add webhook</code><br></p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":181,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-45-1024x431.png" alt="" class="wp-image-181"/></figure>
<!-- /wp:image -->

<!-- wp:image {"id":182,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-46-1024x489.png" alt="" class="wp-image-182"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Put PAT to the <code>Secret</code> section. And put the Jenkins URL, then write an extention path on it as shown below.</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>http:&#47;&#47;&lt;jenkins_url&gt;/github-webhooks/</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p>GitHub will send a request to test if the provided url is working. If successful, it should be like the following picture.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":183,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/image-47.png" alt="" class="wp-image-183"/></figure>
<!-- /wp:image -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Step 9: Create a building, testing, and deploying script for your project</h2>
<!-- /wp:heading -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Go back to Jenkins project, and click <code>Configure</code>.</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:image {"id":184,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/image-48.png" alt="" class="wp-image-184"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>2. Create a build step with the <code>execute shell</code>. Here is an <strong>example</strong> of Hinna booking service project's build script.</p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul class="wp-block-list"><!-- wp:list-item -->
<li>First, we build the Spring Boot application to obtain the execution files. </li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>Second, we remove the existing container and image from the previous build. If that's the first time, those steps will be skipped as there's no previous container or image.</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>Third, we build an image with the Dockerfile.</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>Fourth, we push the image into a container with a specific port.</li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:image {"id":187,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-49-1024x439.png" alt="" class="wp-image-187"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Now, everything has been set up properly. The Jenkins will show the record of every build.<br></p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":188,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://hinna.io/wp-content/uploads/2024/09/image-50.png" alt="" class="wp-image-188"/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>For each build, they provide an option to see a real time console output.</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":189,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://hinna.io/wp-content/uploads/2024/09/image-51-1024x486.png" alt="" class="wp-image-189"/></figure>
<!-- /wp:image -->

<!-- wp:heading -->
<h2 class="wp-block-heading">Resources</h2>
<!-- /wp:heading -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Docker installation <a href="https://docs.docker.com/engine/install/ubuntu/#install-from-a-package" data-type="link" data-id="https://docs.docker.com/engine/install/ubuntu/#install-from-a-package">guide</a> for Ubuntu</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>Jenkins installation <a href="https://www.jenkins.io/doc/book/installing/linux/" data-type="link" data-id="https://www.jenkins.io/doc/book/installing/linux/">guide</a> for Linux</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a href="https://www.youtube.com/watch?v=jSm0YZ-NQAc&amp;t=1142s" data-type="link" data-id="https://www.youtube.com/watch?v=jSm0YZ-NQAc&amp;t=1142s">Jenkins GitHub Integration</a></li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->