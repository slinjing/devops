or create a new repository on the command line
echo "# python1" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/slinjing/python1.git
git push -u origin main