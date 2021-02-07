## Links

[Overview of DSM Architecture] (https://help.ivanti.com/iv/help/en_US/DSM/vNow/Konzept/N_KON_Vogel.htm)

## Offene Fragen

    [ ] Greifen DSM Clients direkt auf die BLS Server zu? Übernehmen die SH die Rolle der Relay Proxy

---

# BLS

## Quality of Service (BLS)

Source: [Quality of Service (BLS)](https://help.ivanti.com/iv/help/en_US/DSM/vNow/Konzept/N_KON_INF_OBJ_MPT_BL_QoS.htm)

>   A BLS handles the following tasks (levels): __ImmediateExecution__, __Provisioning__, __InstallerFinishing__, __Polling__.

>   If a BLS does not have enough resources available, the tasks are delayed (e.g. client synchronization). Then, an error message appears in the log files regarding the QoS plus the respective task (level). If these error messages accumulate for a specific task, the resources can be adjusted accordingly. For further information and assistance, contact DSM Support.

## Multiple BLS

Source: [Multiple BLS](https://help.ivanti.com/iv/help/en_US/DSM/vNow/Konzept/N_KON_INF_OBJ_MPT_BL_MultipleBLS.htm)

### Use as Failover Cluster

- Die Rolle des Primary BLS kann verschoben werden, der Vorgang ist jedoch manuell.
- Nützlich wenn ein Wartungsfenster stattfindet oder wenn der Primary BLS komplett ausfällt
<br>
- [ ] Rolle des Primary BLS manuell verschieben wie?

    [Replacing the Primary BLS](https://help.ivanti.com/iv/help/en_US/DSM/vNow/Aufgaben/N_AUF_INF_MPE_PrimaryBLStauschen.htm)


- [ ] Autofailover implementieren?

> You may use several BLS as failover clusters to ensure the failover of the BLS. If the Primary BLS fails, another BLS can take over its tasks and __can be promoted__ to Primary BLS. By using several BLS you can ensure the high availablity of the DSM environment.
>
> __This is also the case if the BLS requires maintenance work or if you have to update the DSM infrastructure.__
>
> Best Practice:
> In order to quickly replace the Primary BLS Management Point in case it fails, we recommend that you set up all of the BLS Management Points in your DSM environment so that they are identical. In such an instance, you can if needed, __immediately promote any BLS to function as the Primary BLS.__

### Use as Load-Balancing Cluster

> Peak loads usually occur in the morning or evening when most of the managed computers log on to or off from the BLS in the DSM environment. __The load balancing within the DSM environment is effected because the clients select one of the BLS at random listed in the ICDB for their synchronization attempts__.

### Notes on Employing Multiple BLS

- Sind mehrere BLS vorhanden, so wie der BLS, der als erstes aufgesetzt wurde zu primary BLS
<br>
- [ ] Auswirkungen auf die Infrastruktur dokumentieren, wenn der Primary BLS ausfällt

> When using several BLS' if it is absolutely necessary that there is a Primary BLS. When you set up the DSM environment the first time (initial installation), the __BLS on the Central Management Point (the Management Point you installed first) automatically becomes Primary BLS__.

> As long as there is no Primary BLS in your DSM environment, you will be unable to update the DSM environment.
In addition, certain BLS settings in the ICDB can only be set and changed for the Primary BLS. The Primary BLS passes these settings on to the other BLS.

## Load Balancing in a BLS Group

Source: [Load Balancing in a BLS Group](https://help.ivanti.com/iv/help/en_US/DSM/vNow/Konzept/N_KON_INF_OBJ_MPT_BL_LoadBalancing.htm)

### Load balancing Factor

> There are two ways of configuring the system to achieve the optimum coordination:
> - You can define an individual load balancing factor for every BLS , thus allowing you to control the communication between the clients and Relay Proxies on the one side and the BLSs provided in the environment on the other side (ICDB section Shared Infrastructure).

Aktuell ist der Loadbalancing Factor hinterlegt:

Management Point (SWVPS001) -> Load balancing factor: `50`
Management Point (SWVPS002) -> Load balancing factor: `50`
Management Point (SWVPS003) -> Load balancing factor: `100`
Management Point (SWVPS004) -> Load balancing factor: `100`

__Offene Fragen:__

- [x] 🤔 Für was steht BLP?

    BLP steht für Buisness Logic Proxy

- [ ] 🤔 Was macht die Property `List of preffered BLS(s) for communication with this BLP`?


Siehe [Load Balancing Factor](https://help.ivanti.com/iv/help/en_US/DSM/vNow/Einstellungen/N_CTAB_LoadbalancingFactorBLS.htm)

Siehe [Configuring Load Balancing in a BLS Group](https://help.ivanti.com/iv/help/en_US/DSM/vNow/Aufgaben/N_AUF_INF_MPE_MultiBLSLastverteilungKonfigurieren.htm)

### DSMC list

> - You can define a list for the DSMC containing the associated Business Logic Servers (ICDB section DSM Console).

Aktuell ist auf der ORG Ebene folgende Einstellung aktiv:

    ORG -> DSM Console -> List of preferred BLS(s) for communication with the Console:
    - Management Point (SWVPS001).ORG

Ein Ausfall des Primary BLS SWVPS001 hat zur Folge, dass das Bedienen der DSM Console nicht mehr möglich wird.

- [ ] Alle BLS Server in der 'List of preffered BLS(s)' erfassen
- [ ] SWVPS001 herunterfahren
- [ ] Testen ob DSMC funktioniert
<br>
- [ ] Nur den SWVPS002 in der 'List of preffered BLS(s)' erfassen
- [ ] SWVPS002 herunterfahren
- [ ] Testen ob DSMC funktioniert


#### 👀 Zum Ausprobieren:

- [ ] blsURL beim Aufruf der DSM Console mitgeben

* Wenn Primary BLS erreichbart ist
* Wenn Primary BLS nicht erreichbar ist

>Another option is to start the DSMC with a special command line parameter "/BlsUrl":
>
>Example:
>
>Server1 is the Primary BLS Server
>Server2 is a Secondary BLS Server
>
>If Server2 should be used for communication with the DSMC following command line should be used:
>
>\\server\share\dsmc.exe  /BlsUrl:http://Server2:8080/blsAdministration/AdministrationService.asmx
>
>⚠️ Using command line parameter "/BlsUrl" is not supported by Global Support Services and may be used at your own risk. Using Option 1 is the recommended way.

Source: [How to start the DSMC communicating](https://forums.ivanti.com/s/article/How-to-start-the-DSMC-communicating-with-a-fixed-BLS-server?language=en_US)


#### 🤔 Offene Fragen:

- [ ] Was passiert wenn bei der Verbindungsaufbau ein BLS nicht erreichbar ist?

    If a listed BLS fails, the system redirects the communication automatically to another BLS.

- [ ] Was passiert wenn der Primary BLS bei der Verbindungsaufbau nicht erreichbar ist?

Siehe [List of Preferred BLS for the Console's Communication](https://help.ivanti.com/iv/help/en_US/DSM/vNow/Einstellungen/N_CTAB_SelectedBLSCommunicationDSMC.htm)

Siehe [How to start the DSMC communicating with a fixed BLS Server](https://forums.ivanti.com/s/article/How-to-start-the-DSMC-communicating-with-a-fixed-BLS-server?language=en_US)

> In general, load balancing is always active in a BLS group and can be customized accordingly by changing the load balancing factor (values 0 to 100, default setting 100) . You can customize the factor if you have more than 2 Business Logic Servers (BLS) in your environment; however, we recommend customizing the factor only if you have more than 3 BLS .

> The default load balancing behavior is that components always connect to the Business Logic Servers unless the requesting component has its individual setting or unless the requests have to be redirected because of BLS failures.

