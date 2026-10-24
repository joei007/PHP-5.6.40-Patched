# Patch อย่างง่ายสำหรับ CVE-2026-109618 และ CVE-2026-109619 ครับ

## CVE-2026-109618
Patch ตัวนี้จะเปลี่ยนจาก longjmp ไปเป็น _exit เลย เพื่อกันไม่ให้ระบบ Shutdown กลับไปอ่านข้อมูลในโครงสร้างที่ยังสร้างไม่เสร็จ ซึ่งป้องกันไม่ให้เกิดการ RCE ได้ แต่จะเกิดปัญหาใหม่คือ PHP จะข้าม Shutdown function ที่ลงทะเบียนไว้ทั้งหมดและจบการทำงานเลย สำหรับผู้ที่ใช้ PHP-FPM ตัว Master Process จะ Fork Child ใหม่มาแทนตัวที่ปิดไปครับ

## CVE-2026-109619
Patch ตัวนี้จะ Clear ค่าที่เกี่ยวกับ Session ที่ไม่ได้ Clear ก่อนจบ Request เพื่อป้องกัน RCE จาก Bug ได้ อย่างไรก็ดีถ้าส่วนของคุณมี Mod อะไรที่จะต้อง Clear ค่าก่อนจบ Process หรือมีการใช้ PHP_VALUE หรือ PHP_ADMIN_VALUE หรือ มีการใช้ Web Socket ในการเชื่อมต่อ PHP-FPM แทน Unix Socket ก็อาจจะยังมีความเสี่ยงอยู่

## ข้อแนะนำเพิ่มเติม
- ปิด Upload Progress ผ่าน php.ini โดยการเพิ่ม session.upload_progress.enabled=0
- จำกัดขนาด Request ที่ Apache โดยการเพิ่ม LimitRequestBody 20971520 (20MB)
- ลด pm.max_requests ใน php-fpm.conf ลดให้เหลือ 1 เลยก็ได้ ของผมตั้งไว้ที่ 100
