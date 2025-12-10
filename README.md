EE creation

Pour un env docker

Ansible et ansible-builder installés

execution-environement.yml
---
version: 3

images:
  base_image:
    name: registry.redhat.io/ansible-automation-platform-25/ee-minimal-rhel9:latest

dependencies:
  galaxy:
    collections:
      - name: community.docker

  python:
    # On garde uniquement le SDK docker moderne
    - docker>=6.0.0
    - requests
    # SUPPRIMÉ : - docker-compose (C'est lui le coupable)

  system:
    - git
    - python3-pip
    - python3-devel
    - gcc
    # Si tu as besoin de fonctionnalités Docker Compose, il vaut mieux installer le plugin CLI
    # Note : Cela dépend si le paquet est dispo dans les repos de ton image de base.
    # Sinon, le module community.docker standard (docker_container) suffit pour 99% des cas.

options:
  package_manager_path: /usr/bin/microdnf



Ensuite lancer : 
ansible-builder build --tag mon-ee-docker:2.5 --container-runtime podman -v 3



Vérification : 
Complete! The build context can be found at: /home/lab-user/context
[lab-user@bastion ~]$ podman run --rm mon-ee-docker:2.5 ansible-galaxy collection list

# /usr/share/ansible/collections/ansible_collections
Collection                               Version
---------------------------------------- -------
community.docker                         5.0.4
community.library_inventory_filtering_v1 1.1.5





Envoyer l’image sur un repo

[lab-user@bastion ~]$ podman push mon-ee-docker:2.5 quay.io/rh-ee-djakubow/mon-ee-docker:2.5
Getting image source signatures
Error: trying to reuse blob sha256:004d2c90a65694c2830b06fddc1047d40063c6cb36fb31a5a3edfce9435326c6 at destination: checking whether a blob sha256:004d2c90a65694c2830b06fddc1047d40063c6cb36fb31a5a3edfce9435326c6 exists in quay.io/rh-ee-djakubow/mon-ee-docker: authentication required
[lab-user@bastion ~]$



Il faut s’identifier avant
[lab-user@bastion ~]$ podman login quay.io
Username: rh-ee-djakubow
Password:
Login Succeeded!
[lab-user@bastion ~]$


[lab-user@bastion ~]$ podman push mon-ee-docker:2.5 quay.io/rh-ee-djakubow/mon-ee-docker:2.5


Dans AAP : 

Créer les creds pour accéder au repo : 


puis créer l’EE

Et l'utiliser dans le template : 



