برای ایجاد یک سرور OpenVPN (ovpn) در دستگاه MikroTik، مراحل زیر را دنبال کنید:

1. **ورود به دستگاه MikroTik**:

   ابتدا وارد پنل مدیریت MikroTik خود شوید.

2. **ایجاد CA (Certificate Authority)**:

   در قسمت System > Certificates، یک CA بسازید. این گواهی نامه برای امضاء گواهی‌نامه‌های دیگر استفاده می‌شود.

   ```
   /certificate
   add name=ca-template common-name=ca days-valid=3650 key-size=2048 key-usage=key-cert-sign,crl-sign
   sign ca-template ca-crl-host=127.0.0.1 name=ca
   ```

3. **ایجاد Server Certificate**:

   سپس یک گواهی نامه برای سرور ایجاد کنید:

   ```
   /certificate
   add name=server-template common-name=myvpn.org days-valid=3650 key-size=2048
   sign server-template ca=ca name=server
   ```

4. **تنظیمات OpenVPN**:

   در قسمت PPP > Interface، یک اتصال PPTP Server بسازید.

   ```
   /interface ovpn-server server
   set auth=sha1 certificate=server cipher=aes128 default-profile=default enabled=yes keepalive-timeout=60 max-mtu=1500 mode=ip netmask=24 port=1194 require-client-certificate=yes
   ```

5. **ایجاد نقطه اتصال برای کلاینت‌ها**:

   سپس یک پروفایل کاربری برای کلاینت‌ها بسازید:

   ```
   /ppp profile
   add change-tcp-mss=yes dns-server=8.8.8.8,8.8.4.4 name=ovpn-profile use-compression=default use-encryption=default use-mpls=default use-vj-compression=default
   ```

6. **اضافه کردن کلاینت‌ها**:

   حالا می‌توانید کلاینت‌ها را به سرور OpenVPN اضافه کنید:

   ```
   /ppp secret
   add name=myuser password=mypassword profile=ovpn-profile
   ```

7. **تنظیم مسیردهی**:

   برای تضمین دسترسی کلاینت‌ها به شبکه‌های محلی، باید مسیردهی را تنظیم کنید.

   ```
   /ip route
   add distance=1 dst-address=192.168.88.0/24 gateway=ovpn-out1
   ```

   در اینجا، `192.168.88.0/24` شبکه‌ی محلی شماست.

8. **بررسی وضعیت OpenVPN**:

   در قسمت PPP > Secrets، می‌توانید کلاینت‌ها و وضعیت اتصالات OpenVPN را ببینید.

   ```
   /ppp secret print
   /interface ovpn-server server print
   ```

9. **دسترسی به شبکه محلی**:

   کلاینت‌های OpenVPN باید از حالت "Redirect Gateway" استفاده کنند تا بتوانند به شبکه‌های محلی دسترسی داشته باشند.

   ```
   /ppp secret set [find name=myuser] remote-address=ovpn-out1
   ```

در نهایت، حالا کلاینت‌های شما می‌توانند از طریق نرم‌افزار OpenVPN به سرور MikroTik شما متصل شوند. اطمینان حاصل کنید که کلاینت‌ها در تنظیمات خود از گواهی نامه‌های CA و سرور استفاده می‌کنند.
