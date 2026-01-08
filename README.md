# ML-project
from PIL import Image, ImageDraw
import numpy as np

class HumanPixelGenerator:
    def render(self, p, low_res=32):
        # Parametri morfologici
        head_w = int(6 + p[0] * 3)
        body_h = int(10 + p[1] * 8)
        body_w = int(7 + p[2] * 4)
        # Colori base
        skin = (int(255 - p[3]*50), int(220 - p[3]*100), int(180 - p[3]*120))
        cloth = (int(p[4]*255), int(p[5]*100), int(p[6]*200))
        # Funzione helper per creare ombre (scurisce il colore del 30%)
        def get_shadow(color):
            return tuple(max(0, int(c * 0.7)) for c in color)
        skin_shadow = get_shadow(skin)
        cloth_shadow = get_shadow(cloth)
        outline = (30, 30, 30) # Grigio quasi nero per i bordi
        img = Image.new('RGB', (low_res, low_res), color=(240, 240, 240))
        draw = ImageDraw.Draw(img)
        cx, cy = low_res // 2, 3
        # --- TESTA ---
        # Outline testa
        draw.rectangle([cx - head_w//2 - 1, cy - 1, cx + head_w//2 + 1, cy + head_w + 1], fill=outline)
        # Base pelle
        draw.rectangle([cx - head_w//2, cy, cx + head_w//2, cy + head_w], fill=skin)
        # Ombra collo/mento
        draw.line([(cx - head_w//2, cy + head_w), (cx + head_w//2, cy + head_w)], fill=skin_shadow)
        # Occhi
        draw.point([(cx - 1, cy + head_w//2), (cx + 1, cy + head_w//2)], fill=(0,0,0))
        cy += head_w + 1
        # --- BUSTO ---
        # Outline busto
        draw.rectangle([cx - body_w//2 - 1, cy, cx + body_w//2 + 1, cy + body_h], fill=outline)
        # Base vestito
        draw.rectangle([cx - body_w//2, cy, cx + body_w//2, cy + body_h], fill=cloth)
        # Ombra laterale (simula luce da sinistra)
        draw.rectangle([cx + body_w//2 - 1, cy, cx + body_w//2, cy + body_h], fill=cloth_shadow)
        # --- BRACCIA ---
        arm_h = body_h // 1.5
        # Braccio sinistro (con outline)
        draw.rectangle([cx - body_w//2 - 2, cy, cx - body_w//2 - 1, cy + arm_h], fill=outline)
        draw.rectangle([cx - body_w//2 - 1, cy, cx - body_w//2 - 1, cy + arm_h - 1], fill=skin)
        # Braccio destro (in ombra)
        draw.rectangle([cx + body_w//2 + 1, cy, cx + body_w//2 + 2, cy + arm_h], fill=outline)
        draw.rectangle([cx + body_w//2 + 1, cy, cx + body_w//2 + 1, cy + arm_h - 1], fill=skin_shadow)
        cy += body_h
        # --- GAMBE ---
        leg_h = 7
        # Gamba SX
        draw.rectangle([cx - body_w//2, cy, cx - body_w//2 + 1, cy + leg_h], fill=outline)
        draw.point([(cx - body_w//2, cy + leg_h)], fill=(10, 10, 10)) # Scarpa
        # Gamba DX
        draw.rectangle([cx + body_w//2 - 1, cy, cx + body_w//2, cy + leg_h], fill=outline)
        draw.point([(cx + body_w//2, cy + leg_h)], fill=(10, 10, 10)) # Scarpa
        # --- UPSCALING ---
        img_display = img.resize((320, 320), resample=Image.NEAREST)
        img_display.show()
        img_display.save("pro_pixel_art.png")

gen = HumanPixelGenerator()
p = np.array([0.5, 0.6, 0.4, 0.1, 0.9, 0.2, 0.2]) # Personaggio rosso
gen.render(p)
