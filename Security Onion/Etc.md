# Everything & Everyone

## Wazuh/Ossec

### Tìm kiếm luật cần modify đang năm trong file rule nào

`grep -rnw '/opt/so/rules/hids/ruleset/rules/' -e 'Rule_id'`

## Suricata

### Disable rule 

`sudo so-rule disabled add 'Rule_id'`