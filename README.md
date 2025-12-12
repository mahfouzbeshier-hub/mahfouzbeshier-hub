# burn_text_on_image.py
# يحفظ صورة جديدة بنص مدموج (لصورة ثابتة - PNG/JPG). يحتاج Pillow.
# pip install pillow
from PIL import Image, ImageDraw, ImageFont
import os

INPUT = "hellocoders_rounded.png"   # مسار الصورة الثابتة
OUTPUT = "hellocoders_with_name.png"
NAME = "Mahfouz Beshier"
SUB = "2nd year CS & AI • Cybersecurity Enthusiast"

# choose font (ستحتاج ملف خط ttf موجود محليًا)
FONT_PATH = "/usr/share/fonts/truetype/dejavu/DejaVuSans-Bold.ttf"
FONT_SIZE = 48
SUB_FONT_SIZE = 20

img = Image.open(INPUT).convert("RGBA")
w, h = img.size

# نص شفاف على منتصف الصورة
overlay = Image.new("RGBA", img.size, (255,255,255,0))
draw = ImageDraw.Draw(overlay)

try:
    font = ImageFont.truetype(FONT_PATH, FONT_SIZE)
    subfont = ImageFont.truetype(FONT_PATH, SUB_FONT_SIZE)
except Exception:
    font = ImageFont.load_default()
    subfont = ImageFont.load_default()

# حساب الموضع المركزي
text_w, text_h = draw.textsize(NAME, font=font)
sub_w, sub_h = draw.textsize(SUB, font=subfont)

x = (w - text_w) // 2
y = (h - text_h) // 2 - 10

# خلفية شفافة للوحة النص
pad_x = 28
pad_y = 12
rect_x0 = x - pad_x
rect_y0 = y - pad_y
rect_x1 = x + text_w + pad_x
rect_y1 = y + text_h + sub_h + pad_y + 6

# رسم مستطيل شبه شفاف
draw.rounded_rectangle([(rect_x0, rect_y0), (rect_x1, rect_y1)],
                       radius=30, fill=(0,0,0,160))

# كتابة النص
draw.text((x, y), NAME, font=font, fill=(255,255,255,255))
draw.text(( (w - sub_w)//2, y + text_h + 6 ), SUB, font=subfont, fill=(220,220,220,230))

# دمج الحواف
result = Image.alpha_composite(img, overlay)
result = result.convert("RGB")
result.save(OUTPUT, quality=95)
print("Saved:", OUTPUT)
