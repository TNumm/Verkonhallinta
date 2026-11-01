1 Ympäristön tarkistaminen
    Dokumentoi
    Lisää raporttiisi lyhyt kuvaus siitä, miten Ansible-palvelin pystyy kommunikoimaan reitittimien kanssa

        Dockerin kautta DNS osoitteiden avulla.

        172.20.20.12    STREAM clab-hamk-verkonhallinta-golden-r1
        172.20.20.6     STREAM clab-hamk-verkonhallinta-golden-r2
        172.20.20.2     STREAM clab-hamk-verkonhallinta-golden-r3


2.SSH-yhteydet ja Ansible-inventaario

    käyttämäsi IP-osoitteet
    ansible-inventory --graph -tulos
        root@ansible:/ansible/automation-lab# ansible-inventory -i inventory/hosts.yml --graph
        @all:
        |--@ungrouped:
        |--@routers:
        |  |--r1-edge
        |  |--r2-core
        |  |--r3-branch


    Ansible-yhteystestin tulos  <-- Käytin tässä tekoälyä, en ollut ihan varma miten ottaa yhteystesti.
        root@ansible:/ansible/automation-lab# ansible routers -i inventory/hosts.yml -m raw -a "hostname" --ask-pass
            SSH password: 
            r1-edge | CHANGED | rc=0 >>
            r1
            Warning: Permanently added 'clab-hamk-verkonhallinta-golden-r1' (ED25519) to the list of known hosts.
            Shared connection to clab-hamk-verkonhallinta-golden-r1 closed.

            r2-core | CHANGED | rc=0 >>
            r2
            Warning: Permanently added 'clab-hamk-verkonhallinta-golden-r2' (ED25519) to the list of known hosts.
            Shared connection to clab-hamk-verkonhallinta-golden-r2 closed.

            r3-branch | CHANGED | rc=0 >>
            r3
            Warning: Permanently added 'clab-hamk-verkonhallinta-golden-r3' (ED25519) to the list of known hosts.
            Shared connection to clab-hamk-verkonhallinta-golden-r3 closed.

3. Verkon automaattinen inventointi

    root@ansible:/ansible/automation-lab# ansible-playbook -i inventory/hosts.yml playbooks/facts.yml --ask-pass ---- Tekoälyä käytetty taas tässäkin apuna.
            
    ok: [r1-edge] => {
        "msg": "Hostname:\nr1\r\n\n\nIP addresses and interfaces:\nlo               UNKNOWN        127.0.0.1/8 ::1/128 \r\neth0@if52        UP             172.20.20.12/24 \r\n\n\nOperating system:\nNAME=\"Alpine Linux\"\r\nID=alpine\r\nVERSION_ID=3.16.2\r\nPRETTY_NAME=\"Alpine Linux v3.16\"\r\nHOME_URL=\"https://alpinelinux.org/\"\r\nBUG_REPORT_URL=\"https://gitlab.alpinelinux.org/alpine/aports/-/issues\"\r\n\n\nUptime:\n 16:28:04 up 54 min,  0 users,  load average: 0.04, 0.06, 0.07\r\n"
    }
    ok: [r2-core] => {
        "msg": "Hostname:\nr2\r\n\n\nIP addresses and interfaces:\nlo               UNKNOWN        127.0.0.1/8 ::1/128 \r\neth0@if51        UP             172.20.20.6/24 \r\n\n\nOperating system:\nNAME=\"Alpine Linux\"\r\nID=alpine\r\nVERSION_ID=3.16.2\r\nPRETTY_NAME=\"Alpine Linux v3.16\"\r\nHOME_URL=\"https://alpinelinux.org/\"\r\nBUG_REPORT_URL=\"https://gitlab.alpinelinux.org/alpine/aports/-/issues\"\r\n\n\nUptime:\n 16:28:04 up 54 min,  0 users,  load average: 0.04, 0.06, 0.07\r\n"
    }
    ok: [r3-branch] => {
        "msg": "Hostname:\nr3\r\n\n\nIP addresses and interfaces:\nlo               UNKNOWN        127.0.0.1/8 ::1/128 \r\neth0@if54        UP             172.20.20.2/24 \r\n\n\nOperating system:\nNAME=\"Alpine Linux\"\r\nID=alpine\r\nVERSION_ID=3.16.2\r\nPRETTY_NAME=\"Alpine Linux v3.16\"\r\nHOME_URL=\"https://alpinelinux.org/\"\r\nBUG_REPORT_URL=\"https://gitlab.alpinelinux.org/alpine/aports/-/issues\"\r\n\n\nUptime:\n 16:28:04 up 54 min,  0 users,  load average: 0.04, 0.06, 0.07\r\n"
    }

    PLAY RECAP *****************************************************************************************************************************************************************************************************************************************************************
    r1-edge                    : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    r2-core                    : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    r3-branch                  : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   



    Kysymys: Mitä eroja havaitset reitittimien interface- ja IP-konfiguraatioissa?
        Reitittimet kuuluvat samaan verkkoon 172.20.20.x. mutta luonnollisesti niillä on omat ip osoitteet.


5. Verkon tila Git-versionhallintaan
    Kysymys: Miksi verkkolaitteiden konfiguraatioita kannattaa säilyttää versionhallinnassa?
    Versionhallinnan avulla voidaan palauttaa hajonnut ympäristö aikaisempaan toimivaan versioon.


6. Hallittu konfiguraatiomuutos
    Kysymys: Mitä tulos kertoo Ansiblen idempotenssista?

    Samaa playbookkia voidaan suorittaa useita kertoja ilman, että jo oikein määritettyjä asetuksia muutetaan uudelleen.
    root@ansible:/ansible/automation-lab# ansible-playbook -i inventory/hosts.yml playbooks/loopback.yml --ask-pass

    r3-branch                  : ok=6    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    r3-branch                  : ok=3    changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0   



7. Muutoksen tarkistaminen
    Kysymys: Miksi muutoksen tekeminen ja sen onnistumisen tarkistaminen ovat automaatiossa eri asioita?

    Eihän muutoksen tekeminen vielä takaa että ne muutokset onnistuisivat. Automaatio voi suorittaa komennot, vaikka lopputulos ei onnistuisikaan.

    r1-edge                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    r2-core                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    r3-branch                  : ok=6    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  


8. Muutoksen tallentaminen
    tuoma@TNKoti:~/Verkonhallinta/configs/ansible/automation-lab$ git log --oneline
    b6b4ce5 (HEAD -> master) Add branch loopback interface
    1c8114e Initial network state


    Mitä hyötyä Ansible tarjoaa verrattuna reitittimien käsin konfigurointiin?
        Ansible mahdollistaa useiden laitteiden konfiguroinnin, ilman että jokaiselle laitteelle pitää kirjautua erikseen

    Mitä Ansiblen idempotenssi tarkoittaa käytännössä?
        Ei muuteta turhaan oikein määritettyjä asetuksia

    Mitä hyötyä Git-versionhallinnasta on verkkolaitteiden hallinnassa?
        Gitissä voi seurata versioden muutoksia ja palata ongelmatapauksissa takaisin toimivaan versioon.

    Miten virtuaalisten FRRouting-reitittimien hallinta eroaa oikeiden verkkolaitteiden hallinnasta?
        Se toimii Linux-ympäristöissä?

    Mitä tapahtui simuloidussa virhetilanteessa ja miten palautit oikean konfiguraation?
    Muutin reitittimen ip osoitteen
        lo10             UNKNOWN        192.0.2.99/32 fe80::4ce8:39ff:fe50:669/64
        palautin oikean ip osoitteen loopback.yml
        -lo10             UNKNOWN        192.0.2.3/32 fe80::4ce8:39ff:fe50:669/64 
        +lo10             UNKNOWN        192.0.2.99/32 fe80::4ce8:39ff:fe50:669/6



Tehtävässä tuli käytettyä raskaasti tekoälyä apuna. En siitä enään loppuvaiheesssa ruvennut jokaisella rivillä erikseen mainitsemaan. Oli paljon ongelmia yhteyksien ja gitin duplikaattien kanssa eri hakemistoissa...
Meni aikaa, hiuksia, sekä hikeä saada kaikki järjellisesti toimimaan. Lopputulos kuitenkin on se mitä haettiin ja kaikki vaiheet sain kokeiltua läpi, vaikkakin tekoälyn avustamana.

