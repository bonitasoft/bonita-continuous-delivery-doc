TODO: lien vers cette page dans celle du deployer et du builder

# Build and deploy a Bonita living application
The Bonita continuous delivery add-on  allows you to easily build and deploy a Bonita living application on a running Bonita platform. It uses the [Bonita living application Builder](livingapp_build.md) and the [Bonita living application Deployer](livingapp_deploy.md).  


##   Bestpractices about repositories

TODO: dire qu'il faut faire un repo par 'projet' (au sens business), eviter les repo ou il y a plein de trucs qui s'entremellent et qui n'ont rien a faire ensemble. Utiliser des outils de versionning type git pour eviter d'avoir a garder 50 versions d'un process dans un meme repo. Expliquer que c'est lie au faiot que le builder buid tout le repo quoiqu'il arrive, donc que plus le repo est gros plus ca prend du temps. 

##   Build and deploy straight

TODO: fonctionnement de base de BCD, checkout un repo build and deploy

##   Build, remove and add some binairies, deploy

TODO: parler de quand il y a des trucs a faire entrte le build et le deploy. Exemple, rajouter des custom pages qui viennent de en dehors mais que l'on souhaite quand meme deployer. Selectionner uniquement une sous partie de ce qui a ete build pour y deployer ( en deplacant tout, en supprimant une partie des trucs buildes ...). 

##   Create a deploy.json, build and deploy

TODO: Meme objectif que avant, en gerant les policies. Attention: utilisation avancee, le format du .json peut etre ammene a evoluer dans les futures versions. -> peut etre dire la meme chose dans la page du deployer.