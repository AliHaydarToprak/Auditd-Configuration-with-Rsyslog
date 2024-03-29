# Auditd Configuration with Rsyslog

## 1-) Debian Based Dist. Auditd Configuration

### 1.1 Paketlerin Yüklenmesi

    sudo apt update -y
    sudo apt install -y auditd audispd-plugins


### 1.2 Auditd Kurallarının Eklenmesi

    sudo vi /etc/audit/rules.d/audit.rules

yukarıdaki dosya açılarak dosyanın içeriği tamamen temizlenir ve aşağıda satırlar dosyanın içerisine kopyalanır.

    ## First rule - delete all
    -D

    ## Increase the buffers to survive stress events.
    ## Make this bigger for busy systems
    -b 8192

    ## Set failure mode to syslog
    -f 1

    ##Sample Rules Here


Not : Yukarıdaki belirtilen "##Sample Rules Here" ile eklenecek kurallar dikkatli seçilmelidir aksi halde büyük miktarda log üretebilir ve sistem performansını etkileyebilir. Bu nedenle, bu tür geniş kapsamlı kuralları kullanmadan önce, sisteminizdeki gereksinimleri dikkatlice değerlendirmeniz ve sadece izlemeniz gereken olayları izlemeye odaklanmanız önemlidir.

İncelemeniz için örnek bırakıyorum : https://github.com/Neo23x0/auditd/blob/master/audit.rules

### 1.3 Auditd Konfigürasyonu
Auditd de logun daha anlaşılabilir olması için logun zenginleştirilmesi aktif edilir.

    sudo vi /etc/audit/auditd.conf

dosya açılarak log_format kısmı kontrol edilir. 
Eğer log_format = RAW ise bu satır log_format = ENRICHED olacak şekilde değiştirilir. Not ubuntu 16 gibi eski versiyonlarda log_format bölümü pas geçilmeli default bırakılmalı

-----------------------------------------------------------------------------------

    sudo vi /etc/audit/plugins.d/syslog.conf 

eğer bu dosya boş ise 
    
    vi /etc/audisp/plugins.d/syslog.conf 
    
dosyası açılarak active = no olması kontrol edilir yes ise no ile değiştirilmelidir.

### 1.4 Rsyslog Konfigürasyonu

    sudo vi /etc/rsyslog.conf

rsyslog konfigürasyon dosyası açılarak var ise daha öncesinden bir qradar yönlendirmesi, o satır yorum hale getirilerek aşağıdaki yeşil ile işaretlenmiş satırlar dosyanın sonuna eklenir.

    # Auditd and all logs send
    $ModLoad imfile
    $InputFileName /var/log/audit/audit.log
    $InputFileTag audit_log
    $InputFileStateFile audit_log-state
    $InputFileFacility local6
    $InputRunFileMonitor

    # Tüm logları belirtilen adres ve porta gönderme
    *.* @@<remote_ip>:<remote_port>


### 1.5 Servislerin Yapılandırılması

    sudo service ufw stop
    sudo systemctl disable ufw
    sudo service auditd restart
    sudo systemctl enable auditd
    sudo systemctl restart rsyslog
    sudo systemctl enable rsyslog

----------------------------------------------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 2-) RHEL/Centos Based Dist. Auditd Configuration

### 1.1 Paketlerin Yüklenmesi

    sudo yum update -y
    sudo yum install -y audispd-plugins

### 1.2 Auditd Kurallarının Eklenmesi

    sudo vi /etc/audit/rules.d/audit.rules

yukarıdaki dosya açılarak dosyanın içeriği tamamen temizlenir ve aşağıda satırlar dosyanın içerisine kopyalanır.

    ## First rule - delete all
    -D

    ## Increase the buffers to survive stress events.
    ## Make this bigger for busy systems
    -b 8192

    ## Set failure mode to syslog
    -f 1

    ##Sample Rules Here

Not : Yukarıdaki belirtilen "##Sample Rules Here" kısmına eklenecek kurallar dikkatli seçilmelidir aksi halde büyük miktarda log üretebilir ve sistem performansını etkileyebilir. Bu nedenle, bu tür geniş kapsamlı kuralları kullanmadan önce, sisteminizdeki gereksinimleri dikkatlice değerlendirmeniz ve sadece izlemeniz gereken olayları izlemeye odaklanmanız önemlidir.

İncelemeniz için örnek bırakıyorum : https://github.com/Neo23x0/auditd/blob/master/audit.rules

### 1.3 Auditd Konfigürasyonu

Auditd de logun daha anlaşılabilir olması için logun zenginleştirilmesi aktif edilir.

    sudo vi /etc/audit/auditd.conf

dosya açılarak log_format kısmı kontrol edilir. 
Eğer log_format = RAW ise bu satır log_format = ENRICHED olacak şekilde değiştirilir.

-----------------------------------------------------------------------------------
Eğer centos 7 ise :

    sudo vi /etc/audisp/plugins.d/syslog.conf

Centos 8 ise :

    sudo vi /etc/audit/plugins.d/syslog.conf

dosya açılarak active = no olması kontrol edilir yes ise no ile değiştirilmelidir.

### 1.4 Rsyslog Konfigürasyonu

    sudo vi /etc/rsyslog.conf

rsyslog konfigürasyon dosyası açılarak var ise daha öncesinden bir qradar yönlendirmesi, o satır yorum hale getirilerek aşağıdaki yeşil ile işaretlenmiş satırlar dosyanın sonuna eklenir.

    # Auditd and all logs send
    $ModLoad imfile
    $InputFileName /var/log/audit/audit.log
    $InputFileTag audit_log
    $InputFileStateFile audit_log-state
    $InputFileFacility local6
    $InputRunFileMonitor

    # Tüm logları belirtilen adres ve porta gönderme
    *.* @@<remote_ip>:<remote_port>


### 1.5 Servislerin Yapılandırılması

    sudo service firewalld stop
    sudo systemctl disable firewalld
    sudo service auditd restart
    sudo systemctl enable auditd
    sudo systemctl restart rsyslog
    sudo systemctl enable rsyslog
