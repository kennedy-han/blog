---
layout: post
title: "��д������"
description: "��д ���"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###beginning
* Windows��ʹ��secureCRT�뿪���崮��ͨ��
    * �½��Ự���ն�ѡ��serial
    * �˿�ѡ���ҵĵ��Թ����У�ʶ�����COM�˿ں�
    * �����ʸ�Ϊ115200
    * ȥ��RTS/CTS��ѡ��
* ��һ��ʹ�ã�������û��u-boot��ʱ��ʹ��˵�����ϵĹ��ߣ�openJTAG��J-Link�ȣ���дu-boot��Nor Flash

* �л���Nor Flash����

* ���ո����u-boot�˵�

* ������ʾѡ��Download u-boot to Nor Flash

* ֮�� Windows�¿�ʹ��dnw���س���u-boot ��win7��������Щ���⣩����ʹ��tftp����dnw

* ʹ��tftp��Ҫ�鿴�����ÿ�����IP ʹ��`print` ����

* ipaddr��serverip���ԣ�Ҫ��tftp��server interface��IP����һ�����Σ�����u-boot�˵���Q�˳���ʹ��
    ```
   set ipaddr 10.0.0.102 //���ÿ�����IP
   set serverip 10.0.0.100 //���ÿ����彫Ҫ���ӵ�server ip
    ```
    
* ֮���ڿ�������ping server ip ��ע�⣺������PC����ping�����壬��Ϊu-boot���ظ���

###�����д4�ַ���
1. ���ڣ�ʹ�ò��ڹ�����д�����ߣ�ʹ��oflash��д�������ϵ�۲�Ч��

2. ʹ��openJTAG��д�����ߣ�ʹ��oflash��д����д����Զ���λ������

3. JLINKֻ����дNOR Flash���պ�u-boot.bin��ʹ��NOR����

4. ʹ��Nor Flash�ϵ�u-boot��д
    * ʹ�ò˵�ͨ��USB������д(�Ӻ�USB������)
    * ʹ��TFTP������д(�Ӻ�����)
        * ��tftp�������ƶ�current DirectoryΪ��׼
        
        * �������� `tftp 3000000 lcd.bin`
            * `help tftp`�鿴ʹ�ù���
        
        * `mtd`�鿴�������
        
        * ʹ��`nand erase bootloader` ����bootloader����
        
        * ʹ��`nand write 30000000 bootloader`����ַ30000000����д��bootloader����
        
        * �ص磬�л�Nand���������ϵ缴�ɿ���Ч��