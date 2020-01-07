# Reference Architecture
The web tier of the Gluu Server (i.e. oxAuth) is stateless and can be scaled horizontally. The local LDAP server included in all Gluu Server deployments (i.e. Gluu LDAP) supports multi-master replication (MMR). Any instance can be written to and changes are propagated to other instances.![CM_Arch](../img/cluster-manager-diagram.png)
