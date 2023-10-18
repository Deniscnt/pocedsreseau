# pocedsreseau
VPC strat reseau poc eds 
# Guide d'Installation 3DS Outscale

Ce guide vous explique les étapes nécessaires pour mettre en place un VPC, un Subnet et un Bucket avec 3DS Outscale.

VPC : Virtual Private Cloud Net (réseau virtuel) dans lesquels vous pouvez lancer des ressources Cloud. Ces ressources doivent être lancées dans des Subnets et peuvent être connectées à internet ou non.
Vous utilisez des route tables pour router le trafic de chaque Subnet, et des security groups pour protéger vos ressources. Les Nets vous permettent également de créer une peering connection avec un autre Net, d’utiliser plusieurs network interfaces pour vos machines virtuelles (VM) et de créer des connexions DirectLink ou VPN.
<img width="273" alt="NetOutscaleSchema" src="https://github.com/Deniscnt/pocedsreseau/assets/147147654/c9ed01cd-9b50-4bf5-b2dc-bb5aa0a7b9d7">

## Étapes

1. **Connexion à l'Interface de Gestion 3DS Outscale**

   Rendez-vous sur le site web de 3DS Outscale et connectez-vous à l'interface de gestion.

2. **Création d'un VPC (NET)**
    
   - Allez dans le tableau de bord.
   - Sélectionnez "Net" dans le menu de navigation.
   - Cliquez sur "Create Net" et suivez les étapes guidées pour configurer votre VPC.
   - Choisir le name : vpc_poc_eds
   - IP range : 192.168.00/16 ## notation CIDR
   - VM tenancy : ne pas cocher la case Dedicated !!! si coché, l'attribut tenancy sera attribué automatiquemeent aux subnets et non modifiable dans le net.
   - Il faut au préalavble , avoir créé une keypair.
     
Après avoir créé un Net, vous devez créer un ou plusieurs Subnets dans celui-ci, les ressources devant être lancées dans des Subnets. Les Subnets correspondent à des sous-réseaux au sein de votre Net, auxquels vous attribuez une plage d’IP en notation CIDR.

**Dans chaque Subnet d’un Net, 3DS OUTSCALE réserve les quatre premières IP ainsi que la dernière du bloc CIDR. Vous ne pouvez pas assigner ces IP à des ressources. Par exemple, dans un Subnet avec le bloc CIDR 10.0.0.0/24, les IP suivantes sont réservées :
10.0.0.0 : L’adresse réseau.
10.0.0.1 : Réservée pour le routeur Net.
10.0.0.2 : Réservée pour le serveur DNS.
10.0.0.3 : Réservée pour un usage futur.
10.0.0.255 : L’adresse de diffusion réseau.
**Vous pouvez également utiliser Elastic Identity Management (EIM) pour contrôler qui peut accéder, créer et gérer des ressources dans votre Net.
**DirectLink : Vous pouvez mettre en place une connexion physique entre votre réseau internet et un site DirectLink où se trouve votre Net pour accéder directement à vos ressources. Pour en savoir plus, voir DirectLink.

**Net Peering : Vous pouvez appairer deux Nets pour autoriser les ressources dans chacun d’eux à communiquer entre elles. Pour en savoir plus, voir Travailler avec les Net peerings.
Une instance, pour laquelle vous pouvez spécifier l’OUTSCALE machine image (OMI) à utiliser, le type d’instance, la keypair et l’IP externe (EIP) attachée à celle-ci. Vous pouvez aussi choisir d’ajouter un tag "osc.fcu.eip.auto-attach" à l’instance pour fixer automatiquement l’EIP à travers le processus d’arrêt et de démarrage

3. **Création d'un Subnet**
Chaque Subnet doit être associé à une route table afin de spécifier les routes autorisées pour le trafic sortant qui quitte le Subnet.
   - Allez dans le tableau de bord.
   - Sélectionnez l'id du net , "Subnets" dans le menu de navigation.
   - Cliquez sur "Créer un Subnet" et suivez les étapes pour configurer votre Subnet.
   - Taper le nom du subnet
   - Plage IP appartenant au bloc CIDR du Net
   - Puis la region
   - Créer une route table pour controler le traffic des subnets, chaque route table contient la route local qui route le trafic ciblant une IP du bloc CIDR du Net localement. Cette route ne peut être ni modifiée ni supprimée
   - Possibilité de faire les memes etapes via l'OCS CLI 
  Chaque Subnet d’un Net doit être associé à une route table, qui contrôle le routage pour toutes les machines virtuelles (VM) placées dans ce Subnet
  De la même manière qu’il est recommandé de dédier un Subnet à une seule application, il est également recommandé d’utiliser une route table et un security group par     Subnet.
**Lier une route à un subnet
  Cliquez à l’intérieur du dashboard Route Tables pour faire apparaître des cases à cocher.
  Cochez la case de la route table que vous souhaitez lier à un Subnet.
  Cliquez sur IconLink Lier à un Subnet.
  La boîte de dialogue LIER LA ROUTE TABLE AU SUBNET apparaît.
  Dans la liste, sélectionnez le Subnet que vous souhaitez lier à la route table.
  Cliquez sur Lier à un Subnet.
  La route table est liée au Subnet.

4. **Security group**

Les security groups vous permettent de contrôler l’accès à vos VM dans votre Net. Les security groups agissent comme un ensemble de règles de firewall qui, dans un Net, contrôlent les flux entrants et sortants. Lorsque vous lancez des VM dans un Net, vous devez les associer à un ou plusieurs security groups. Si vous ne spécifiez aucun security group, la VM est automatiquement associée avec le security group par défaut qui est créé automatiquement avec votre Net.

5. **Création d'un Bucket**

   - Allez dans le tableau de bord.
   - Sélectionnez "Stockage" dans le menu de navigation.
   - Cliquez sur "Créer un Bucket" et suivez les étapes pour configurer votre Bucket.

6. **Configurer les Autorisations**

   Assurez-vous de configurer les autorisations appropriées pour votre Bucket afin de définir qui peut y accéder et quelles actions ils peuvent effectuer.
   

7. **Utilisation dans votre Application**

   Vous pouvez maintenant utiliser votre VPC, Subnet et Bucket dans votre application en utilisant les informations fournies par 3DS Outscale.

## Commandes CLI

Voici quelques commandes CLI utiles pour gérer vos ressources avec l'outil en ligne de commande de 3DS Outscale :

- **Lister les VPCs** :
  ```bash
  osc-vpc DescribeVpcs

- **Créer un VPC :
  ```bash
  osc-vpc CreateVpc --CidrBlock <CIDR_BLOCK>
  
- **Lister les Subnets :
  ```bash
  osc-vpc DescribeSubnets

- **Créer un Subnet** :
  ```bash
  osc-vpc CreateSubnet --VpcId <VPC_ID> --CidrBlock <CIDR_BLOCK>

- **Lister les Buckets :
  ```bash
  osc-s3 DescribeBuckets
  
- **Créer un Bucket :
  ```bash
  osc-s3 CreateBucket --BucketName <BUCKET_NAME>