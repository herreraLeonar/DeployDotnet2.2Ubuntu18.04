# DeployDotnet2.2Ubuntu18.04
deplying dotnet core 2.2 into ubuntu 18.04 with apache2 and pm2

# Deploy net core 2.2 in AWS Instance ubuntu 18.04 apache2 pm2

# Add into startup.cs

´´ Function configure(){
  app.UseForwardedHeaders(new ForwardedHeadersOptions
            {
                ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
            });
            app.UseHttpsRedirection();

            //authentication is important position above UseMvc
            app.UseAuthentication();
}´´
# in Startup.ConfigureServices:
``services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});``

# Console Commands to apache2
sudo su
sudo apt-get update
sudo apt-get install apache2

# Remenber clone your projects into /var/www/
# Git clone https://yourproject.git

# commands to dotnet

wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb

sudo dpkg -i packages-microsoft-prod.deb
sudo add-apt-repository universe
sudo apt-get update
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install dotnet-sdk-2.2
sudo add-apt-repository universe
sudo apt-get update
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install aspnetcore-runtime-2.2
in folder repository run
dotnet publish --configuration Release

# install nodejs en Ubuntu
curl -sL https://deb.nodesource.com/setup_13.x | sudo -E bash -
sudo apt-get install nodejs

# install pm2
sudo apt-get -y install python3-pip
sudo pip3 install flask

npm install pm2 -g

# command modules apache2
sudo a2enmod proxy
sudo a2enmod proxy_http
edit apache conection
sudo nano /etc/apache2/sites-enables/000-default.conf
copy paste into:
<VirtualHost *:80>
``ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
</VirtualHost>``

sudo service apache2 restart


# commands to run myapp into Release folder
pm2 start "dotnet yourproject.dll --urls http://0.0.0.0:5000" --name test

sudo service apache2 restart

