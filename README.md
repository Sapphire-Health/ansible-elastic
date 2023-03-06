## Deploy winlogbeat on windows servers

### Create an inventory

```
cat << EOF > hosts.yml
all:
    hosts:
        ansibletest01.fqdn.tld:
vars:
    ansible_user: "username@FQDN.TLD"
    ansible_connection: winrm
    ansible_winrm_transport: kerberos
    ansible_winrm_server_cert_validation: ignore
EOF
```

### Create a variable file

```
cat << EOF > vars.yml
match_host: ansibletest01.fqdn.tld
package_url: http://webserverwithmsi/
config_dir: "C:/ProgramData/Elastic/Beats/winlogbeat"
package_name: winlogbeat-8.6.2-windows-x86_64.msi
product_id: "{2DE7C165-4793-5EE1-9DF0-372072955E2B}"
winlogbeat_config: winlogbeat.yml.j2
logstash_server: logstash.fqdn.tld:5044
#uninstall: "yes"
install_params: ""
EOF
```

### Get a kerberos ticket
```
kinit username@DOMAIN.TLD
```

### Run the playbook
```
ansible-playbook -i hosts.yml -e @vars.yml deploy-winlogbeat.yml
```

### Sample Powershell script to enable specific Windows Event Logs
```
$logName = 'Microsoft-Windows-PrintService/Operational'
$log = New-Object System.Diagnostics.Eventing.Reader.EventLogConfiguration $logName
$log.IsEnabled=$true
$log.SaveChanges()
```