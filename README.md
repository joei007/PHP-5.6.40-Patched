# Patch อย่างง่ายสำหรับ PHP 5.6.40 สำหรับแก้ไข CVE-2026-109618 และ CVE-2026-109619

เฉพาะ PHP 5.6.40 เท่านั้น และเฉพาะ PHP-FPM เท่านั้นด้วยครับ

## CVE-2026-109618
Patch ตัวนี้จะจะทำงานเมื่อเกิด OOM (Out of Memory) โดยจะเปลี่ยนจาก longjmp ไปเป็น _exit เลย เพื่อกันไม่ให้ระบบ Shutdown กลับไปอ่านข้อมูลในโครงสร้างที่ยังสร้างไม่เสร็จ ซึ่งป้องกันไม่ให้เกิดการ RCE ได้ แต่จะเกิดปัญหาใหม่คือ PHP จะข้าม Shutdown function ที่ลงทะเบียนไว้ทั้งหมดและจบการทำงานเลย

## CVE-2026-109619
Patch นี้จะมี 2 ไฟล์ โดย Patch แรกนี้จะ Clear ค่าที่เกี่ยวกับ Session ที่ไม่ได้ Clear ก่อนจบ Request ส่วน Patch ที่ 2 จะกำหนดว่าถ้าการรัน Script เจอ Fatal Error ก็ให้ฆ่า Child นั้นทิ้งหลังจาก Worker รันเสร็จเพื่อไม่ให้มี State ค้างสำหรับการเจาะระบบผ่าน Working เดิม 

## ข้อแนะนำเพิ่มเติม
- ปิด Upload Progress ผ่าน php.ini โดยการเพิ่ม session.upload_progress.enabled=0
- จำกัดขนาด Request ที่ Apache โดยการเพิ่ม LimitRequestBody 20971520 (20MB)
- ลด pm.max_requests ใน php-fpm.conf ลดให้เหลือ 1 เลยก็ได้ ของผมตั้งไว้ที่ 100
- หลังจาก Patch แล้วจะเจอว่า Child Process ของ PHP-FPM ตายเป็นระยะ ซึ่งเป็นเรื่องปกติ
