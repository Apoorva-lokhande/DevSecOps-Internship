# Setting up MKDocs

MkDocs is a static site generator that's geared towards building project documentation. Documentation source files are written in Markdown, and configured with a single YAML configuration file. MkDocs will build your docs and used to commit and push them to GitHub.

## Installing MKDocs

I have installed MKDocs using the official [Documentation](https://www.mkdocs.org/user-guide/installation/) 

## Selecting a Theme

In order to customize a theme, I referred official [Documentation](https://github.com/mkdocs/mkdocs/wiki/MkDocs-Themes) from MkDocs themes. Here I preferred `Material` theme, to use this particular theme it needs to be installed via pip. 

    pip install mkdocs-material

## Configuration

Create a new project to store all the documentation as given in below commands:
     
    mkdocs new my-project
    cd my-project

After creating an folder in VScode, create a file named `mkdocs.yml` and a folder named `docs`.  

`The index.md` file is created in docs folder which contains a single documentation page.

In `mkdocs.yml` include:

    `site_name: 'DevSecOps-pipeline'
    nav:
    Introduction: 'index.md'
    Contents: 'contents.md'
    Setting up VM: 'setting-up-VM.md'
    Setting up Jenkins: 'setting-up-jenkins.md'
    Setting up mkDocs: 'setting-up-mkdocs.md'

    theme: material`

Here we have mentioned the site name as **DevSecOps-pipeline** which is the title of the site, in the **nav** include all the contents which are explained througout the documentation and the **theme** as we have downloaded before needs to be mentioned here.

## Push code to GitHub

Here, I made a repository initially as `DevSecOps-Internship` also checked the option `private repository` and then create `repository`.

In a terminal, I ran:

git init
mkdocs build
git add .
git commit -m "Initial commit"
git push -u origin master

Later I checked the repository all the files where not commited.

I figured this out by checkiing the contents.md file, where I forgot to add the files that were created. I again commited the files using the commands as above, now the files were successfully commited.

## Building the MKDocs site

Building a site converts the documented files into a HTML and CSS format and will the files in a folder named `site` within the same directory which contains `mkdocs.yml` file.

In the terminal i ran a following command froma parent directory:

    mkdocs build --clean

## Deploy on Netlify

- Created an account in [netlify](https://www.netlify.com/).
- Selected an `Add a new project` option.
- A window will open which asks for `Connect to git provider`.
- Select `Create a new site` and later select `Github`.
- Later, select the repository you need to  publish, i selected DevSecOps-pipeline.
- later select the repository and a window will appear under that add `site/` in publish directory.

  ![image](/pictures/github-site.png)

- Click on Deploy site and the site will bw deployed and the link will bw available on top of the screen.
   
   ![image](/pictures/linkk.png)
