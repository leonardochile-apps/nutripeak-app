[index.html](https://github.com/user-attachments/files/28226359/index.html)
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>NUTRIPEAK - Control Nutricional Inteligente</title>
  <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.9.1/chart.min.js"></script>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background: #F2F7F4; min-height: 100vh; color: #111; }
    input, select, textarea, button { font-family: inherit; font-size: 14px; outline: none; }
    input, select, textarea { border: 1.5px solid #E0E0E0; border-radius: 10px; padding: 9px 12px; background: #fff; color: #111; width: 100%; transition: border-color .15s; }
    input:focus, select:focus, textarea:focus { border-color: #2D9C6B; }
    button { cursor: pointer; }
    ::-webkit-scrollbar { width: 4px; }
    ::-webkit-scrollbar-thumb { background: #ccc; border-radius: 4px; }
  </style>
</head>
<body>
<div id="root"></div>
<script type="text/babel">
const { useState, useEffect, useRef, useMemo } = React;

/* ── BASE DE DATOS ─────────────────────────────────────────────── */
const DB_BASE = [
  { id:1,  n:"Pechuga de pollo (cocida) - Av. Central",    cal:165,pro:31, car:0,  fat:3.6,u:"g",  cat:"🥩 Proteínas" },
  { id:2,  n:"Muslo de pollo (cocido) - Caja Negra",      cal:209,pro:26, car:0,  fat:11, u:"g",  cat:"🥩 Proteínas" },
  { id:3,  n:"Carne molida (magra) - Don Juan",         cal:215,pro:26, car:0,  fat:12, u:"g",  cat:"🥩 Proteínas" },
  { id:4,  n:"Filete de vacuno - Santa Isabel",             cal:250,pro:28, car:0,  fat:15, u:"g",  cat:"🥩 Proteínas" },
  { id:5,  n:"Atún en agua - Soprole/Jumbo",     cal:116,pro:26, car:0,  fat:1,  u:"g",  cat:"🥩 Proteínas" },
  { id:6,  n:"Salmón - Multiexport/Aqua Mas",                       cal:208,pro:20, car:0,  fat:13, u:"g",  cat:"🥩 Proteínas" },
  { id:7,  n:"Merluza - Expres Pescado",                      cal:92, pro:18, car:0,  fat:2,  u:"g",  cat:"🥩 Proteínas" },
  { id:8,  n:"Huevo entero - Prodal/Huevo Santa María",      cal:70, pro:6,  car:0.4,fat:5,  u:"un", cat:"🥩 Proteínas",base:50 },
  { id:10, n:"Cerdo lomo - Don Juan",                   cal:242,pro:27, car:0,  fat:14, u:"g",  cat:"🥩 Proteínas" },
  { id:11, n:"Jamón de pavo - Persana/Politono",                cal:104,pro:15, car:1,  fat:4,  u:"g",  cat:"🥩 Proteínas" },
  { id:20, n:"Leche entera - Soprole/Lider",                 cal:61, pro:3.2,car:4.8,fat:3.3,u:"ml", cat:"🥛 Lácteos" },
  { id:21, n:"Leche descremada - Colun",             cal:34, pro:3.4,car:5,  fat:0.1,u:"ml", cat:"🥛 Lácteos" },
  { id:22, n:"Yogur natural - Jumbo/Tottus",   cal:59, pro:3.5,car:4,  fat:3.3,u:"g",  cat:"🥛 Lácteos" },
  { id:23, n:"Yogur descremado - Soprole",             cal:38, pro:4,  car:5,  fat:0.5,u:"g",  cat:"🥛 Lácteos" },
  { id:24, n:"Queso fresco - Quesería Maguilú",                 cal:172,pro:12, car:1.4,fat:13, u:"g",  cat:"🥛 Lácteos" },
  { id:25, n:"Queso mantecoso - Santa Isabel",              cal:330,pro:22, car:0,  fat:27, u:"g",  cat:"🥛 Lácteos" },
  { id:26, n:"Quesillo - Soprole",                     cal:130,pro:15, car:1,  fat:7,  u:"g",  cat:"🥛 Lácteos" },
  { id:30, n:"Arroz blanco - Mahatma/Aralda",        cal:130,pro:2.7,car:28, fat:0.3,u:"g",  cat:"🌾 Cereales" },
  { id:31, n:"Arroz integral - Aralda/Jumbo",      cal:111,pro:2.6,car:23, fat:0.9,u:"g",  cat:"🌾 Cereales" },
  { id:32, n:"Pan marraqueta - Amasandería local",    cal:164,pro:5.5,car:32, fat:1.5,u:"un", cat:"🌾 Cereales",base:65 },
  { id:33, n:"Pan integral - Bimbo/Ideal",    cal:80, pro:4,  car:15, fat:1,  u:"un", cat:"🌾 Cereales",base:35 },
  { id:34, n:"Pasta cocida - Lider Brand",                 cal:131,pro:5,  car:25, fat:1.1,u:"g",  cat:"🌾 Cereales" },
  { id:35, n:"Avena (cruda)",                cal:389,pro:17, car:66, fat:7,  u:"g",  cat:"🌾 Cereales" },
  { id:36, n:"Papa cocida",                  cal:87, pro:1.9,car:20, fat:0.1,u:"g",  cat:"🌾 Cereales" },
  { id:37, n:"Camote cocido",                cal:90, pro:2,  car:21, fat:0.1,u:"g",  cat:"🌾 Cereales" },
  { id:38, n:"Quinoa cocida",                cal:120,pro:4.4,car:21, fat:1.9,u:"g",  cat:"🌾 Cereales" },
  { id:39, n:"Galleta de arroz (1 unidad)",  cal:35, pro:0.7,car:7,  fat:0.3,u:"un", cat:"🌾 Cereales",base:9 },
  { id:50, n:"Porotos cocidos",              cal:127,pro:8.7,car:23, fat:0.5,u:"g",  cat:"🫘 Legumbres" },
  { id:51, n:"Lentejas cocidas",             cal:116,pro:9,  car:20, fat:0.4,u:"g",  cat:"🫘 Legumbres" },
  { id:52, n:"Garbanzos cocidos",            cal:164,pro:8.9,car:27, fat:2.6,u:"g",  cat:"🫘 Legumbres" },
  { id:53, n:"Arvejas cocidas",              cal:84, pro:5.4,car:15, fat:0.4,u:"g",  cat:"🫘 Legumbres" },
  { id:60, n:"Lechuga",                      cal:15, pro:1.4,car:2.9,fat:0.2,u:"g",  cat:"🥦 Verduras" },
  { id:61, n:"Tomate",                       cal:18, pro:0.9,car:3.9,fat:0.2,u:"g",  cat:"🥦 Verduras" },
  { id:62, n:"Zanahoria",                    cal:41, pro:0.9,car:10, fat:0.2,u:"g",  cat:"🥦 Verduras" },
  { id:63, n:"Brócoli",                      cal:34, pro:2.8,car:7,  fat:0.4,u:"g",  cat:"🥦 Verduras" },
  { id:64, n:"Espinaca",                     cal:23, pro:2.9,car:3.6,fat:0.4,u:"g",  cat:"🥦 Verduras" },
  { id:65, n:"Pepino",                       cal:16, pro:0.7,car:3.6,fat:0.1,u:"g",  cat:"🥦 Verduras" },
  { id:66, n:"Palta (1/2 unidad)",           cal:120,pro:1.5,car:6,  fat:11, u:"un", cat:"🥦 Verduras",base:75 },
  { id:67, n:"Cebolla",                      cal:40, pro:1.1,car:9,  fat:0.1,u:"g",  cat:"🥦 Verduras" },
  { id:68, n:"Pimentón rojo",                cal:31, pro:1,  car:6,  fat:0.3,u:"g",  cat:"🥦 Verduras" },
  { id:69, n:"Zapallo cocido",               cal:26, pro:1,  car:6.5,fat:0.1,u:"g",  cat:"🥦 Verduras" },
  { id:70, n:"Coliflor",                     cal:25, pro:1.9,car:5,  fat:0.3,u:"g",  cat:"🥦 Verduras" },
  { id:71, n:"Betarraga cocida",             cal:44, pro:1.7,car:10, fat:0.2,u:"g",  cat:"🥦 Verduras" },
  { id:80, n:"Manzana (1 unidad med.)",      cal:95, pro:0.5,car:25, fat:0.3,u:"un", cat:"🍎 Frutas",base:182 },
  { id:81, n:"Plátano (1 unidad med.)",      cal:105,pro:1.3,car:27, fat:0.4,u:"un", cat:"🍎 Frutas",base:118 },
  { id:82, n:"Naranja (1 unidad med.)",      cal:62, pro:1.2,car:15, fat:0.2,u:"un", cat:"🍎 Frutas",base:131 },
  { id:83, n:"Pera (1 unidad med.)",         cal:101,pro:0.7,car:27, fat:0.2,u:"un", cat:"🍎 Frutas",base:178 },
  { id:84, n:"Frutilla",                     cal:32, pro:0.7,car:7.7,fat:0.3,u:"g",  cat:"🍎 Frutas" },
  { id:85, n:"Uva",                          cal:69, pro:0.7,car:18, fat:0.2,u:"g",  cat:"🍎 Frutas" },
  { id:86, n:"Sandía",                       cal:30, pro:0.6,car:7.6,fat:0.2,u:"g",  cat:"🍎 Frutas" },
  { id:87, n:"Durazno (1 unidad med.)",      cal:58, pro:1.4,car:14, fat:0.4,u:"un", cat:"🍎 Frutas",base:147 },
  { id:88, n:"Kiwi (1 unidad)",              cal:42, pro:0.8,car:10, fat:0.4,u:"un", cat:"🍎 Frutas",base:69 },
  { id:100,n:"Almendras",                    cal:579,pro:21, car:22, fat:50, u:"g",  cat:"🥜 Frutos secos" },
  { id:101,n:"Nueces",                       cal:654,pro:15, car:14, fat:65, u:"g",  cat:"🥜 Frutos secos" },
  { id:102,n:"Maní (sin sal)",               cal:567,pro:26, car:16, fat:49, u:"g",  cat:"🥜 Frutos secos" },
  { id:103,n:"Aceite de oliva (1 cdta)",     cal:40, pro:0,  car:0,  fat:4.5,u:"un", cat:"🥜 Frutos secos",base:4.5 },
  { id:110,n:"Cazuela de vacuno (1 plato)",  cal:280,pro:22, car:28, fat:8,  u:"pl", cat:"🇨🇱 Cocina chilena",base:400 },
  { id:111,n:"Empanada de pino (1 unidad)",  cal:380,pro:18, car:42, fat:16, u:"un", cat:"🇨🇱 Cocina chilena",base:180 },
  { id:112,n:"Pastel de choclo (1 porción)", cal:310,pro:14, car:38, fat:11, u:"pl", cat:"🇨🇱 Cocina chilena",base:350 },
  { id:113,n:"Charquicán (1 plato)",         cal:260,pro:18, car:30, fat:8,  u:"pl", cat:"🇨🇱 Cocina chilena",base:350 },
  { id:114,n:"Porotos con riendas",          cal:220,pro:12, car:38, fat:4,  u:"g",  cat:"🇨🇱 Cocina chilena" },
  { id:115,n:"Ensalada chilena",             cal:35, pro:1,  car:7,  fat:0.5,u:"g",  cat:"🇨🇱 Cocina chilena" },
  { id:116,n:"Sopaipilla (1 unidad)",        cal:170,pro:3,  car:24, fat:7,  u:"un", cat:"🇨🇱 Cocina chilena",base:70 },
  { id:119,n:"Sopa de fideos (1 taza)",      cal:100,pro:3.5,car:18, fat:1.5,u:"pl", cat:"🇨🇱 Cocina chilena",base:250 },
  { id:130,n:"Agua",                         cal:0,  pro:0,  car:0,  fat:0,  u:"ml", cat:"🥤 Bebidas" },
  { id:131,n:"Té (sin azúcar)",              cal:2,  pro:0,  car:0.5,fat:0,  u:"ml", cat:"🥤 Bebidas" },
  { id:132,n:"Café negro (sin azúcar)",      cal:5,  pro:0.3,car:0.5,fat:0,  u:"ml", cat:"🥤 Bebidas" },
  { id:133,n:"Jugo de naranja natural",      cal:45, pro:0.7,car:10, fat:0.2,u:"ml", cat:"🥤 Bebidas" },
  { id:135,n:"Bebida cola (regular)",        cal:42, pro:0,  car:11, fat:0,  u:"ml", cat:"🥤 Bebidas" },
  { id:140,n:"Chocolate negro 70% (1 cuad)", cal:55, pro:0.7,car:4,  fat:4,  u:"un", cat:"🍫 Snacks",base:10 },
  { id:141,n:"Granola (sin azúcar)",         cal:420,pro:11, car:64, fat:15, u:"g",  cat:"🍫 Snacks" },
  { id:142,n:"Barrita de cereal",            cal:120,pro:2,  car:22, fat:3,  u:"un", cat:"🍫 Snacks",base:28 },
  { id:145,n:"Hummus",                       cal:177,pro:8,  car:20, fat:8,  u:"g",  cat:"🍫 Snacks" },
  { id:146,n:"Mix de frutos secos",          cal:500,pro:13, car:27, fat:42, u:"g",  cat:"🍫 Snacks" },
  { id:147,n:"Té con leche (sin azúcar)",    cal:25, pro:1,  car:2,  fat:1.5,u:"ml", cat:"🥤 Bebidas",base:200 },
  
  /* ── COMIDA RÁPIDA ── */
  { id:200,n:"Hamburguesa simple",           cal:540,pro:28, car:40, fat:28, u:"un", cat:"🍔 Comida rápida",base:215 },
  { id:201,n:"Hamburguesa doble",            cal:750,pro:42, car:45, fat:42, u:"un", cat:"🍔 Comida rápida",base:300 },
  { id:202,n:"Hamburguesa con queso",        cal:600,pro:30, car:42, fat:32, u:"un", cat:"🍔 Comida rápida",base:240 },
  { id:203,n:"Sándwich de pollo",            cal:450,pro:25, car:42, fat:18, u:"un", cat:"🍔 Comida rápida",base:180 },
  { id:204,n:"Sándwich de atún",             cal:380,pro:22, car:38, fat:16, u:"un", cat:"🍔 Comida rápida",base:160 },
  { id:205,n:"Sándwich de lomo",             cal:520,pro:28, car:42, fat:24, u:"un", cat:"🍔 Comida rápida",base:200 },
  { id:206,n:"Sándwich jamón y queso",       cal:420,pro:18, car:42, fat:18, u:"un", cat:"🍔 Comida rápida",base:170 },
  { id:207,n:"Hot dog",                      cal:290,pro:10, car:28, fat:15, u:"un", cat:"🍔 Comida rápida",base:95 },
  { id:208,n:"Papas fritas medianas",        cal:320,pro:3,  car:38, fat:17, u:"pl", cat:"🍔 Comida rápida",base:120 },
  { id:209,n:"Papas fritas grandes",         cal:490,pro:5,  car:58, fat:25, u:"pl", cat:"🍔 Comida rápida",base:180 },
  { id:210,n:"Pizza (2 slices)",             cal:590,pro:22, car:70, fat:24, u:"pl", cat:"🍔 Comida rápida",base:300 },
  { id:211,n:"Tacos (3 unidades)",           cal:420,pro:18, car:48, fat:18, u:"un", cat:"🍔 Comida rápida",base:150 },
  { id:212,n:"Burrito",                      cal:520,pro:20, car:58, fat:20, u:"un", cat:"🍔 Comida rápida",base:220 },
  { id:213,n:"Wrap de pollo",                cal:450,pro:24, car:48, fat:16, u:"un", cat:"🍔 Comida rápida",base:180 },
  { id:214,n:"Nuggets de pollo (6 piezas)",  cal:280,pro:14, car:18, fat:16, u:"un", cat:"🍔 Comida rápida",base:100 },
  { id:215,n:"Alitas de pollo (4 piezas)",   cal:320,pro:28, car:4,  fat:20, u:"un", cat:"🍔 Comida rápida",base:100 },
  { id:216,n:"Aros de cebolla (porción)",    cal:380,pro:4,  car:48, fat:18, u:"pl", cat:"🍔 Comida rápida",base:120 },
  { id:217,n:"Pollo frito muslo",            cal:320,pro:28, car:6,  fat:20, u:"un", cat:"🍔 Comida rápida",base:100 },
  { id:218,n:"Pollo frito pechuga",          cal:280,pro:32, car:4,  fat:14, u:"un", cat:"🍔 Comida rápida",base:100 },
  { id:219,n:"Donuts",                       cal:250,pro:3,  car:32, fat:12, u:"un", cat:"🍔 Comida rápida",base:60 },
  { id:220,n:"Sándwich tostado",             cal:420,pro:16, car:45, fat:18, u:"un", cat:"🍔 Comida rápida",base:170 },
  
  /* ── COMIDA CHILENA (expandida) ── */
  { id:250,n:"Completo chileno",             cal:480,pro:18, car:48, fat:24, u:"un", cat:"🇨🇱 Comida chilena",base:200 },
  { id:251,n:"Choripán",                     cal:420,pro:16, car:42, fat:22, u:"un", cat:"🇨🇱 Comida chilena",base:180 },
  { id:252,n:"Empanada de carne",            cal:300,pro:12, car:28, fat:16, u:"un", cat:"🇨🇱 Comida chilena",base:150 },
  { id:253,n:"Empanada de queso",            cal:280,pro:10, car:27, fat:15, u:"un", cat:"🇨🇱 Comida chilena",base:140 },
  { id:254,n:"Empanada de mariscos",         cal:320,pro:14, car:26, fat:18, u:"un", cat:"🇨🇱 Comida chilena",base:160 },
  { id:256,n:"Pastel de verduras",           cal:140,pro:5,  car:16, fat:7,  u:"pl", cat:"🇨🇱 Comida chilena",base:180 },
  { id:257,n:"Arrollado huaso",              cal:220,pro:18, car:8,  fat:12, u:"un", cat:"🇨🇱 Comida chilena",base:100 },
  { id:258,n:"Panqueques",                   cal:250,pro:8,  car:32, fat:10, u:"un", cat:"🇨🇱 Comida chilena",base:100 },
  { id:259,n:"Humita",                       cal:240,pro:7,  car:28, fat:12, u:"un", cat:"🇨🇱 Comida chilena",base:120 },
  { id:260,n:"Ceviche de congrio",           cal:120,pro:21, car:4,  fat:2,  u:"pl", cat:"🇨🇱 Comida chilena",base:200 },
  { id:261,n:"Picante de congrio",           cal:180,pro:22, car:8,  fat:7,  u:"pl", cat:"🇨🇱 Comida chilena",base:250 },
  { id:262,n:"Curanto (1 porción)",          cal:200,pro:18, car:10, fat:10, u:"pl", cat:"🇨🇱 Comida chilena",base:300 },
  { id:263,n:"Sopaipilla con pebre",         cal:320,pro:6,  car:38, fat:16, u:"un", cat:"🇨🇱 Comida chilena",base:150 },
  { id:265,n:"Carbonada (1 plato)",          cal:170,pro:14, car:14, fat:7,  u:"pl", cat:"🇨🇱 Comida chilena",base:300 },
  { id:266,n:"Pastel de pollo",              cal:200,pro:12, car:18, fat:10, u:"pl", cat:"🇨🇱 Comida chilena",base:250 },
  { id:268,n:"Pulmay (pastel trigo)",        cal:280,pro:10, car:32, fat:14, u:"un", cat:"🇨🇱 Comida chilena",base:120 },
  { id:269,n:"Chupe de camarones",           cal:140,pro:14, car:8,  fat:6,  u:"pl", cat:"🇨🇱 Comida chilena",base:250 },
  { id:270,n:"Jurel a la chilena",           cal:180,pro:24, car:4,  fat:8,  u:"pl", cat:"🇨🇱 Comida chilena",base:200 },
  { id:271,n:"Merluza a lo pobre",           cal:220,pro:20, car:16, fat:10, u:"pl", cat:"🇨🇱 Comida chilena",base:300 },
  { id:272,n:"Cazuela de ave (1 plato)",     cal:150,pro:16, car:12, fat:6,  u:"pl", cat:"🇨🇱 Comida chilena",base:400 },
  { id:273,n:"Cazuela de vacuno",            cal:160,pro:14, car:13, fat:8,  u:"pl", cat:"🇨🇱 Comida chilena",base:400 },
  { id:274,n:"Cazuela de mariscos",          cal:140,pro:17, car:11, fat:5,  u:"pl", cat:"🇨🇱 Comida chilena",base:350 },
  { id:275,n:"Mote con huesillo",            cal:180,pro:4,  car:40, fat:2,  u:"tza", cat:"🇨🇱 Comida chilena",base:350 },

  /* ── SUPLEMENTOS DEPORTIVOS (Chile) ── */
  { id:300,n:"Whey Protein (1 scoop)",       cal:120,pro:25, car:2,  fat:1,  u:"un", cat:"💪 Suplementos",base:30 },
  { id:301,n:"Whey Protein + Carbos",        cal:180,pro:20, car:15, fat:2,  u:"un", cat:"💪 Suplementos",base:45 },
  { id:302,n:"BCAA (Energy Drink)",          cal:20, pro:0,  car:5,  fat:0,  u:"ml", cat:"💪 Suplementos",base:250 },
  { id:303,n:"Creatina Monohidrato",         cal:0,  pro:0,  car:0,  fat:0,  u:"g",  cat:"💪 Suplementos" },
  { id:304,n:"Glutamina",                    cal:4,  pro:1,  car:0,  fat:0,  u:"g",  cat:"💪 Suplementos" },
  { id:305,n:"Pre-Entreno (típico)",         cal:15, pro:0,  car:3,  fat:0,  u:"un", cat:"💪 Suplementos",base:5 },
  { id:306,n:"Barrita proteica",             cal:220,pro:20, car:24, fat:6,  u:"un", cat:"💪 Suplementos",base:65 },
  { id:307,n:"Batido de proteína casero",    cal:280,pro:30, car:20, fat:8,  u:"ml", cat:"💪 Suplementos",base:300 },
  { id:308,n:"Colágeno (polvo)",             cal:40, pro:9,  car:0,  fat:0,  u:"g",  cat:"💪 Suplementos" },
  { id:309,n:"Omega-3 (cápsula)",            cal:9,  pro:0,  car:0,  fat:1,  u:"un", cat:"💪 Suplementos",base:1 },
  { id:310,n:"Magnesio (tableta)",           cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"💪 Suplementos",base:1 },
  { id:311,n:"Vitamina D3 (gotas)",          cal:0,  pro:0,  car:0,  fat:0,  u:"ml", cat:"💪 Suplementos",base:1 },
  { id:312,n:"Zinc (tableta)",               cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"💪 Suplementos",base:1 },
  { id:313,n:"Vitamina C (tableta)",         cal:4,  pro:0,  car:1,  fat:0,  u:"un", cat:"💪 Suplementos",base:1 },
  { id:314,n:"Calcio (polvo)",               cal:8,  pro:2,  car:0,  fat:0,  u:"g",  cat:"💪 Suplementos" },
  { id:315,n:"Hierro (gota)",                cal:0,  pro:0,  car:0,  fat:0,  u:"ml", cat:"💪 Suplementos",base:1 },
  { id:316,n:"Multivitamínico",              cal:5,  pro:0,  car:1,  fat:0,  u:"un", cat:"💪 Suplementos",base:1 },
  { id:317,n:"Proteína vegana",              cal:115,pro:24, car:3,  fat:1,  u:"un", cat:"💪 Suplementos",base:30 },
  { id:318,n:"Gainer (ganador de peso)",     cal:400,pro:20, car:60, fat:5,  u:"un", cat:"💪 Suplementos",base:100 },
  { id:319,n:"Vitamina B12 (tableta)",       cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"💪 Suplementos",base:1 },
  
  /* ── VITAMINAS Y MINERALES ── */
  { id:350,n:"Vitamina A (retinol) 1000IU",  cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"🧪 Vitaminas",base:1 },
  { id:351,n:"Vitamina E 400IU",             cal:4,  pro:0,  car:0,  fat:0,  u:"un", cat:"🧪 Vitaminas",base:1 },
  { id:352,n:"Vitamina K2",                  cal:0,  pro:0,  car:0,  fat:0,  u:"ug", cat:"🧪 Vitaminas" },
  { id:353,n:"Ácido fólico 400mcg",          cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"🧪 Vitaminas",base:1 },
  { id:354,n:"Niacina 500mg",                cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"🧪 Vitaminas",base:1 },
  { id:355,n:"Selenio 200mcg",               cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"🧪 Vitaminas",base:1 },
  { id:356,n:"Potasio 400mg",                cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"🧪 Vitaminas",base:1 },
  { id:357,n:"Fósforo",                      cal:0,  pro:0,  car:0,  fat:0,  u:"mg", cat:"🧪 Vitaminas" },
  { id:358,n:"Manganeso",                    cal:0,  pro:0,  car:0,  fat:0,  u:"mg", cat:"🧪 Vitaminas" },
  { id:359,n:"Cobre",                        cal:0,  pro:0,  car:0,  fat:0,  u:"ug", cat:"🧪 Vitaminas" },
  { id:360,n:"Boro",                         cal:0,  pro:0,  car:0,  fat:0,  u:"mg", cat:"🧪 Vitaminas" },
  { id:361,n:"Yodo 150mcg",                  cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"🧪 Vitaminas",base:1 },
  { id:362,n:"Molibdeno",                    cal:0,  pro:0,  car:0,  fat:0,  u:"ug", cat:"🧪 Vitaminas" },
  { id:363,n:"Cromo 200mcg",                 cal:0,  pro:0,  car:0,  fat:0,  u:"un", cat:"🧪 Vitaminas",base:1 },
];

const MEALS   = ["Desayuno","Almuerzo","Once","Cena","Snack"];
const GREEN   = "#2D9C6B";
const BLUE    = "#3B82F6";
const LBLUE   = "#EFF6FF";
const LGREEN  = "#E8F5EE";
const MNS     = ["Ene","Feb","Mar","Abr","May","Jun","Jul","Ago","Sep","Oct","Nov","Dic"];
const DAYS    = ["Dom","Lun","Mar","Mié","Jue","Vie","Sáb"];

const dk  = d => `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,"0")}-${String(d.getDate()).padStart(2,"0")}`;
const ad  = (d,n) => { const r=new Date(d); r.setDate(r.getDate()+n); return r; };
const t0  = () => { const d=new Date(); d.setHours(0,0,0,0); return d; };
const fmt = d => `${d.getDate()} ${MNS[d.getMonth()]} ${d.getFullYear()}`;
const rnd = v => Math.round(v*10)/10;

/* ── Anillo SVG ─────────────────────────────── */
function Ring({pct, size=84, stroke=9, color=GREEN, label, sub}) {
  const r=( size-stroke)/2, circ=2*Math.PI*r, offset=circ*(1-Math.min(pct,1));
  return (
    <div style={{position:"relative",width:size,height:size,flexShrink:0}}>
      <svg width={size} height={size} style={{transform:"rotate(-90deg)"}}>
        <circle cx={size/2} cy={size/2} r={r} fill="none" stroke="#EEF7F2" strokeWidth={stroke}/>
        <circle cx={size/2} cy={size/2} r={r} fill="none" stroke={pct>1?"#EF4444":color}
          strokeWidth={stroke} strokeDasharray={circ} strokeDashoffset={offset}
          strokeLinecap="round" style={{transition:"stroke-dashoffset .4s"}}/>
      </svg>
      <div style={{position:"absolute",inset:0,display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center"}}>
        <div style={{fontSize:14,fontWeight:800,color:pct>1?"#EF4444":color,lineHeight:1}}>{label}</div>
        {sub && <div style={{fontSize:9,color:"#aaa",marginTop:1}}>{sub}</div>}
      </div>
    </div>
  );
}

/* ── Barra macro ─────────────────────────────── */
function MacroBar({label,val,max,color}) {
  const p=Math.min(val/Math.max(max,1),1);
  return (
    <div style={{marginBottom:7}}>
      <div style={{display:"flex",justifyContent:"space-between",fontSize:11,marginBottom:3}}>
        <span style={{color:"#666"}}>{label}</span>
        <span style={{fontWeight:600,color:"#222"}}>{Math.round(val)}g <span style={{color:"#bbb",fontWeight:400}}>/ {max}g</span></span>
      </div>
      <div style={{height:5,background:"#eee",borderRadius:99,overflow:"hidden"}}>
        <div style={{height:"100%",width:`${p*100}%`,background:p>1?"#EF4444":color,borderRadius:99,transition:"width .4s"}}/>
      </div>
    </div>
  );
}

/* ── Mini sparkline peso ─────────────────────── */
function WeightChart({data}) {
  if (data.filter(d=>d!=null).length < 2) return null;
  const vals = data.map(d=>d??null);
  const valid = vals.filter(v=>v!==null);
  const mn=Math.min(...valid)-1, mx=Math.max(...valid)+1;
  const W=160, H=40;
  const pts = vals.map((v,i)=>{
    if(v===null) return null;
    const x = (i/(vals.length-1))*W;
    const y = H - ((v-mn)/(mx-mn||1))*H;
    return [x,y];
  }).filter(Boolean);
  const path = pts.map((p,i)=>`${i===0?"M":"L"} ${p[0]} ${p[1]}`).join(" ");
  return (
    <svg width={W} height={H} style={{display:"block"}}>
      <path d={path} fill="none" stroke={GREEN} strokeWidth={2} strokeLinecap="round" strokeLinejoin="round"/>
      {pts.map(([x,y],i)=>(
        <circle key={i} cx={x} cy={y} r={2.5} fill={GREEN}/>
      ))}
    </svg>
  );
}

/* ── APP ─────────────────────────────────────── */
function App() {
  const [date,      setDate]      = useState(()=>t0());
  const [log,       setLog]       = useState({});
  const [goal,      setGoal]      = useState(2000);
  const [waterGoal, setWaterGoal] = useState(8);
  const [customDB,  setCustomDB]  = useState([]);
  const [modal,     setModal]     = useState(null);
  const [meal,      setMeal]      = useState("Desayuno");
  const [graphView, setGraphView] = useState("diario");
  const [timerSeconds, setTimerSeconds] = useState(0);
  const [timerRunning, setTimerRunning] = useState(false);
  const timerRef = useRef(null);
  const [query,     setQuery]     = useState("");
  const [selFood,   setSelFood]   = useState(null);
  const [amount,    setAmount]    = useState(100);
  const [detEntry,  setDetEntry]  = useState(null);
  const [tab,       setTab]       = useState("hoy");
  const [newFood,   setNewFood]   = useState({n:"",cal:"",pro:"",car:"",fat:"",cat:"⭐ Mis alimentos",u:"g"});
  const [userProfile, setUserProfile] = useState({weight:75,height:170,age:30,sex:"M",activityLevel:1.5,objective:"maintenance"});
  const searchRef = useRef(null);

  /* ── carga ── */
  useEffect(()=>{
    try{const s=localStorage.getItem("cal_log2"); if(s) setLog(JSON.parse(s));}catch{}
    try{const g=localStorage.getItem("cal_goal"); if(g) setGoal(Number(g));}catch{}
    try{const w=localStorage.getItem("cal_wgoal"); if(w) setWaterGoal(Number(w));}catch{}
    try{const c=localStorage.getItem("cal_custom"); if(c) setCustomDB(JSON.parse(c));}catch{}
    try{const p=localStorage.getItem("cal_profile"); if(p) { const prof = JSON.parse(p); setUserProfile(prof); recalculateGoal(prof); }}catch{}
  },[]);

  /* ── Cronómetro ── */
  useEffect(()=>{
    if(timerRunning) {
      timerRef.current = setInterval(()=>setTimerSeconds(s=>s+1), 1000);
    } else {
      if(timerRef.current) clearInterval(timerRef.current);
    }
    return ()=>{if(timerRef.current) clearInterval(timerRef.current);};
  },[timerRunning]);

  const DB = useMemo(()=>[...DB_BASE,...customDB],[customDB]);

  /* ── Cálculo inteligente de calorías (Mifflin-St Jeor) ── */
  const recalculateGoal = (profile) => {
    const {weight, height, age, sex, activityLevel, objective} = profile;
    
    // Fórmula Mifflin-St Jeor
    let bmr;
    if(sex === "M") {
      bmr = 10 * weight + 6.25 * height - 5 * age + 5;
    } else {
      bmr = 10 * weight + 6.25 * height - 5 * age - 161;
    }
    
    const tdee = bmr * activityLevel;
    let dailyGoal;
    
    switch(objective) {
      case "weightLoss":
        dailyGoal = Math.round(tdee * 0.85); // 15% déficit
        break;
      case "muscleGain":
        dailyGoal = Math.round(tdee * 1.1); // 10% superávit
        break;
      default: // maintenance
        dailyGoal = Math.round(tdee);
    }
    
    setGoal(dailyGoal);
    localStorage.setItem("cal_goal", dailyGoal);
  };

  const saveLog = l => { setLog(l); localStorage.setItem("cal_log2",JSON.stringify(l)); };
  const saveCustom = c => { setCustomDB(c); localStorage.setItem("cal_custom",JSON.stringify(c)); };

  const dateKey = dk(date);
  const dayData = log[dateKey] || { entries:[], water:0, weight:null };
  const entries = dayData.entries || [];
  const water   = dayData.water   || 0;
  const weight  = dayData.weight  || "";

  const updateDay = (patch) => {
    const updated = {...log, [dateKey]: {...dayData, ...patch}};
    saveLog(updated);
  };

  const totals = useMemo(()=>entries.reduce((a,e)=>({
    cal:a.cal+e.cal, pro:a.pro+e.pro, car:a.car+e.car, fat:a.fat+e.fat
  }),{cal:0,pro:0,car:0,fat:0}),[entries]);

  /* búsqueda */
  const filtered = useMemo(()=>{
    if(!query.trim()) return [];
    const q=query.toLowerCase();
    return DB.filter(f=>f.n.toLowerCase().includes(q)).slice(0,14);
  },[query,DB]);

  const selectFood = f => { setSelFood(f); setAmount(f.base||100); setQuery(f.n); };

  const calcEntry = (food,amt) => {
    const base=food.base||100, factor=amt/base;
    return { id:Date.now().toString(), foodId:food.id, name:food.n, meal, amount:amt, unit:food.u,
      cal:Math.round(food.cal*factor), pro:rnd(food.pro*factor),
      car:rnd(food.car*factor), fat:rnd(food.fat*factor) };
  };

  const addEntry = () => {
    if(!selFood) return;
    updateDay({ entries:[...entries, calcEntry(selFood,amount)] });
    setModal(null); setQuery(""); setSelFood(null); setAmount(100);
  };

  const removeEntry = id => {
    updateDay({ entries: entries.filter(e=>e.id!==id) });
    setDetEntry(null); setModal(null);
  };

  /* agua */
  const setWater = n => updateDay({ water: Math.max(0,Math.min(n,20)) });

  /* peso */
  const setWeightVal = v => updateDay({ weight: v });

  /* historial peso últimos 14 días */
  const weightHistory = useMemo(()=>Array.from({length:14},(_,i)=>{
    const d=ad(t0(),-13+i), k=dk(d);
    return (log[k]?.weight) ? Number(log[k].weight) : null;
  }),[log]);

  /* historial calorías 7 días */
  const calHistory = useMemo(()=>Array.from({length:7},(_,i)=>{
    const d=ad(t0(),-6+i), k=dk(d);
    const ents=log[k]?.entries||[];
    return { d, k, cal:ents.reduce((s,e)=>s+e.cal,0) };
  }),[log]);

  /* agregar alimento personalizado */
  const saveNewFood = () => {
    const {n,cal,pro,car,fat,cat,u} = newFood;
    if(!n.trim()||!cal) { alert("Nombre y calorías son obligatorios"); return; }
    const food = { id:"c"+Date.now(), n:n.trim(), cal:Number(cal), pro:Number(pro)||0,
      car:Number(car)||0, fat:Number(fat)||0, cat, u, custom:true };
    saveCustom([...customDB, food]);
    setNewFood({n:"",cal:"",pro:"",car:"",fat:"",cat:"⭐ Mis alimentos",u:"g"});
    setModal("add");
  };

  const deleteCustom = id => {
    if(!confirm("¿Eliminar este alimento de tu lista?")) return;
    saveCustom(customDB.filter(f=>f.id!==id));
  };

  const pct = totals.cal/goal;
  const rem = goal - Math.round(totals.cal);

  const openAdd = m => {
    setMeal(m||"Desayuno"); setModal("add");
    setTimeout(()=>searchRef.current?.focus(),120);
  };

  return (
    <div style={{minHeight:"100vh",background:"#F2F7F4"}}>

      {/* TOPBAR */}
      <div style={{background:GREEN,color:"#fff",padding:"12px 16px 0"}}>
        <div style={{fontSize:10,opacity:.7,letterSpacing:".1em",textTransform:"uppercase"}}>NUTRIPEAK</div>
        <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginTop:2,marginBottom:10,gap:8}}>
          <button onClick={()=>setModal("goal")} style={{background:"rgba(255,255,255,.2)",border:"none",borderRadius:99,padding:"4px 12px",color:"#fff",fontSize:12}}>
            Meta: {goal} kcal
          </button>
          <div style={{fontSize:15,fontWeight:700}}>Mi registro diario</div>
          <div style={{display:"flex",gap:6}}>
            <button onClick={()=>setModal("feedback")} style={{background:"rgba(255,255,255,.2)",border:"none",borderRadius:99,padding:"4px 10px",color:"#fff",fontSize:12,cursor:"pointer"}}>
              💬 Feedback
            </button>
            <button onClick={()=>setModal("creditos")} style={{background:"rgba(255,255,255,.2)",border:"none",borderRadius:99,padding:"4px 10px",color:"#fff",fontSize:12,cursor:"pointer"}}>
              ℹ️ Info
            </button>
          </div>
        </div>
        <div style={{display:"flex",gap:4}}>
          {[["hoy","Hoy"],["historial","Historial"],["mis","Mis alimentos"],["timer","Cronómetro"]].map(([k,l])=>(
            <button key={k} onClick={()=>setTab(k)} style={{
              background:tab===k?"rgba(255,255,255,.25)":"transparent",
              border:"none",borderRadius:"8px 8px 0 0",padding:"6px 14px",
              color:"#fff",fontSize:12,fontWeight:tab===k?700:400
            }}>{l}</button>
          ))}
        </div>
      </div>

      {/* ══════ TAB HOY ══════ */}
      {tab==="hoy" && (
        <div style={{padding:14}}>

          {/* Nav fecha */}
          <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:14}}>
            <button onClick={()=>setDate(d=>ad(d,-1))} style={{background:"#fff",border:"1px solid #ddd",borderRadius:8,padding:"6px 12px",fontSize:14}}>‹</button>
            <div style={{textAlign:"center"}}>
              <div style={{fontSize:14,fontWeight:600}}>{fmt(date)}</div>
              {dk(date)===dk(t0())&&<div style={{fontSize:11,color:GREEN,fontWeight:600}}>Hoy</div>}
            </div>
            <button onClick={()=>setDate(d=>ad(d,1))} disabled={dk(date)===dk(t0())}
              style={{background:"#fff",border:"1px solid #ddd",borderRadius:8,padding:"6px 12px",fontSize:14,opacity:dk(date)===dk(t0())?.3:1}}>›</button>
          </div>

          {/* ── Resumen calorías ── */}
          <div style={{background:"#fff",borderRadius:16,padding:16,marginBottom:12,display:"flex",alignItems:"center",gap:14,boxShadow:"0 1px 8px rgba(0,0,0,.07)"}}>
            <Ring pct={pct} label={Math.round(totals.cal)} sub="kcal"/>
            <div style={{flex:1}}>
              <div style={{fontSize:12,color:"#555",marginBottom:8}}>
                {rem>=0
                  ? <><span style={{fontWeight:700,color:GREEN}}>{rem}</span> kcal restantes</>
                  : <><span style={{fontWeight:700,color:"#EF4444"}}>{Math.abs(rem)}</span> kcal sobre meta</>}
              </div>
              <MacroBar label="Proteínas"     val={totals.pro} max={Math.round(goal*.15/4)} color="#3B82F6"/>
              <MacroBar label="Carbohidratos" val={totals.car} max={Math.round(goal*.55/4)} color="#F97316"/>
              <MacroBar label="Grasas"        val={totals.fat} max={Math.round(goal*.30/9)} color="#8B5CF6"/>
            </div>
          </div>

          {/* ── Agua + Peso ── */}
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:10,marginBottom:12}}>

            {/* AGUA */}
            <div style={{background:"#fff",borderRadius:14,padding:14,boxShadow:"0 1px 6px rgba(0,0,0,.06)"}}>
              <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:10}}>
                <div>
                  <div style={{fontSize:11,color:"#888"}}>Agua</div>
                  <div style={{fontSize:17,fontWeight:700,color:BLUE}}>{water}<span style={{fontSize:11,fontWeight:400,color:"#aaa"}}>/{waterGoal}</span></div>
                  <div style={{fontSize:10,color:"#aaa"}}>{water*250} ml</div>
                </div>
                <div style={{fontSize:28}}>💧</div>
              </div>
              {/* Vasos */}
              <div style={{display:"flex",flexWrap:"wrap",gap:4,marginBottom:10}}>
                {Array.from({length:waterGoal},(_,i)=>(
                  <button key={i} onClick={()=>setWater(i<water?i:i+1)}
                    style={{width:28,height:28,borderRadius:6,border:"none",fontSize:16,
                      background:i<water?LBLUE:"#f5f5f5",cursor:"pointer",
                      transition:"background .15s"}}>
                    {i<water?"💧":"○"}
                  </button>
                ))}
              </div>
              <div style={{display:"flex",gap:4}}>
                <button onClick={()=>setWater(water-1)} style={{flex:1,padding:"5px 0",background:"#f5f5f5",border:"none",borderRadius:7,fontSize:16,color:BLUE}}>−</button>
                <button onClick={()=>setWater(water+1)} style={{flex:1,padding:"5px 0",background:LBLUE,border:"none",borderRadius:7,fontSize:16,color:BLUE}}>+</button>
              </div>
            </div>

            {/* PESO */}
            <div style={{background:"#fff",borderRadius:14,padding:14,boxShadow:"0 1px 6px rgba(0,0,0,.06)"}}>
              <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:8}}>
                <div>
                  <div style={{fontSize:11,color:"#888"}}>Peso corporal</div>
                  <div style={{fontSize:17,fontWeight:700,color:GREEN}}>
                    {weight||"—"}<span style={{fontSize:11,fontWeight:400,color:"#aaa"}}> kg</span>
                  </div>
                </div>
                <div style={{fontSize:28}}>⚖️</div>
              </div>
              <input type="number" value={weight} step="0.1" min="30" max="300"
                placeholder="ej: 78.5"
                onChange={e=>setWeightVal(e.target.value)}
                style={{marginBottom:8,textAlign:"center",fontWeight:700,fontSize:15,color:GREEN}}/>
              <div style={{overflow:"hidden"}}>
                <WeightChart data={weightHistory}/>
                <div style={{fontSize:9,color:"#ccc",marginTop:2}}>Últimas 2 semanas</div>
              </div>
            </div>
          </div>

          {/* ── Registro por comida ── */}
          {MEALS.map(m=>{
            const mE=entries.filter(e=>e.meal===m);
            const mC=mE.reduce((s,e)=>s+e.cal,0);
            return (
              <div key={m} style={{background:"#fff",borderRadius:14,marginBottom:10,overflow:"hidden",boxShadow:"0 1px 6px rgba(0,0,0,.06)"}}>
                <div style={{padding:"10px 14px",display:"flex",alignItems:"center",justifyContent:"space-between",borderBottom:mE.length?"1px solid #f5f5f5":"none"}}>
                  <div>
                    <div style={{fontSize:13,fontWeight:600}}>{m}</div>
                    {mC>0&&<div style={{fontSize:11,color:"#999"}}>{Math.round(mC)} kcal</div>}
                  </div>
                  <button onClick={()=>openAdd(m)} style={{background:LGREEN,border:"none",borderRadius:8,padding:"5px 12px",color:GREEN,fontSize:13,fontWeight:600}}>+ Agregar</button>
                </div>
                {mE.map(e=>(
                  <div key={e.id} onClick={()=>{setDetEntry(e);setModal("detail");}}
                    style={{padding:"8px 14px",borderBottom:"1px solid #fafafa",display:"flex",alignItems:"center",gap:10,cursor:"pointer"}}>
                    <div style={{flex:1}}>
                      <div style={{fontSize:13}}>{e.name}</div>
                      <div style={{fontSize:11,color:"#bbb"}}>{e.amount}{["g","ml"].includes(e.unit)?e.unit:""} · P:{e.pro}g C:{e.car}g G:{e.fat}g</div>
                    </div>
                    <div style={{fontWeight:700,color:GREEN,fontSize:14}}>{e.cal}</div>
                    <div style={{fontSize:11,color:"#ccc"}}>kcal</div>
                  </div>
                ))}
              </div>
            );
          })}
        </div>
      )}

      {/* ══════ TAB HISTORIAL ══════ */}
      {tab==="historial" && (
        <div style={{padding:14}}>
          {/* Calorías */}
          <div style={{background:"#fff",borderRadius:16,padding:16,marginBottom:12,boxShadow:"0 1px 8px rgba(0,0,0,.07)"}}>
            <div style={{fontSize:13,fontWeight:600,marginBottom:12}}>Calorías — últimos 7 días</div>
            {calHistory.map(({d,k,cal})=>(
              <div key={k} onClick={()=>{setDate(d);setTab("hoy");}}
                style={{display:"flex",alignItems:"center",gap:12,padding:"9px 0",borderBottom:"1px solid #f8f8f8",cursor:"pointer"}}>
                <div style={{width:44,fontSize:12}}>
                  <div style={{fontWeight:dk(d)===dk(t0())?700:400,color:dk(d)===dk(t0())?GREEN:"#333"}}>{DAYS[d.getDay()]}</div>
                  <div style={{fontSize:10,color:"#bbb"}}>{d.getDate()}/{d.getMonth()+1}</div>
                </div>
                <div style={{flex:1,height:8,background:"#eee",borderRadius:99,overflow:"hidden"}}>
                  <div style={{height:"100%",width:`${Math.min(cal/goal,1)*100}%`,background:cal>goal?"#EF4444":GREEN,borderRadius:99}}/>
                </div>
                <div style={{fontSize:13,fontWeight:600,color:cal>goal?"#EF4444":"#333",minWidth:72,textAlign:"right"}}>
                  {cal>0?`${Math.round(cal)} kcal`:<span style={{color:"#ddd",fontWeight:400}}>—</span>}
                </div>
              </div>
            ))}
            <div style={{marginTop:12,background:"#F2F7F4",borderRadius:10,padding:"10px 12px",display:"flex",justifyContent:"space-between"}}>
              <div style={{fontSize:12,color:"#555"}}>Promedio</div>
              <div style={{fontSize:15,fontWeight:700,color:GREEN}}>
                {Math.round(calHistory.filter(h=>h.cal>0).reduce((s,h)=>s+h.cal,0)/Math.max(calHistory.filter(h=>h.cal>0).length,1))||0} kcal
              </div>
            </div>
          </div>

          {/* Agua histórico */}
          <div style={{background:"#fff",borderRadius:16,padding:16,marginBottom:12,boxShadow:"0 1px 8px rgba(0,0,0,.07)"}}>
            <div style={{fontSize:13,fontWeight:600,marginBottom:12}}>Agua — últimos 7 días</div>
            {calHistory.map(({d,k})=>{
              const w=log[k]?.water||0;
              return (
                <div key={k} style={{display:"flex",alignItems:"center",gap:12,padding:"8px 0",borderBottom:"1px solid #f8f8f8"}}>
                  <div style={{width:44,fontSize:12}}>
                    <div style={{color:dk(d)===dk(t0())?BLUE:"#333"}}>{DAYS[d.getDay()]}</div>
                    <div style={{fontSize:10,color:"#bbb"}}>{d.getDate()}/{d.getMonth()+1}</div>
                  </div>
                  <div style={{display:"flex",gap:3,flex:1}}>
                    {Array.from({length:waterGoal},(_,i)=>(
                      <div key={i} style={{width:12,height:12,borderRadius:3,background:i<w?LBLUE:"#f0f0f0",border:`1px solid ${i<w?"#93C5FD":"#e5e5e5"}`}}/>
                    ))}
                  </div>
                  <div style={{fontSize:13,fontWeight:600,color:BLUE,minWidth:40,textAlign:"right"}}>{w>0?`${w*250}ml`:<span style={{color:"#ddd",fontWeight:400}}>—</span>}</div>
                </div>
              );
            })}
          </div>

          {/* Peso histórico */}
          <div style={{background:"#fff",borderRadius:16,padding:16,boxShadow:"0 1px 8px rgba(0,0,0,.07)"}}>
            <div style={{fontSize:13,fontWeight:600,marginBottom:12}}>Peso corporal — últimas 2 semanas</div>
            <div style={{marginBottom:12}}>
              <WeightChart data={weightHistory}/>
            </div>
            {calHistory.map(({d,k})=>{
              const w=log[k]?.weight;
              return w?(
                <div key={k} style={{display:"flex",justifyContent:"space-between",padding:"6px 0",borderBottom:"1px solid #f8f8f8",fontSize:13}}>
                  <span style={{color:"#666"}}>{DAYS[d.getDay()]} {d.getDate()}/{d.getMonth()+1}</span>
                  <span style={{fontWeight:600,color:GREEN}}>{w} kg</span>
                </div>
              ):null;
            })}
          </div>
        </div>
      )}

      {/* ══════ TAB MIS ALIMENTOS ══════ */}
      {tab==="mis" && (
        <div style={{padding:14}}>
          {/* Sub-tabs dentro de "mis alimentos" */}
          <div style={{display:"flex",gap:6,marginBottom:14,borderBottom:"2px solid #eee",paddingBottom:10}}>
            <button onClick={()=>setGraphView("mis-personalizados")} 
              style={{background:graphView==="mis-personalizados"?GREEN:"transparent",color:graphView==="mis-personalizados"?"#fff":"#666",border:"none",borderRadius:8,padding:"8px 12px",fontSize:13,fontWeight:graphView==="mis-personalizados"?700:400,cursor:"pointer"}}>
              📝 Mis alimentos
            </button>
            <button onClick={()=>setGraphView("mis-base")} 
              style={{background:graphView==="mis-base"?GREEN:"transparent",color:graphView==="mis-base"?"#fff":"#666",border:"none",borderRadius:8,padding:"8px 12px",fontSize:13,fontWeight:graphView==="mis-base"?700:400,cursor:"pointer"}}>
              📚 Base de datos
            </button>
          </div>

          {/* Vista: Mis alimentos personalizados */}
          {graphView==="mis-personalizados" && (
            <>
              <button onClick={()=>setModal("nuevo")} style={{
                width:"100%",marginBottom:14,padding:13,
                background:GREEN,color:"#fff",border:"none",borderRadius:12,
                fontSize:14,fontWeight:700,display:"flex",alignItems:"center",justifyContent:"center",gap:8
              }}>
                <span style={{fontSize:20,lineHeight:1}}>+</span> Crear nuevo alimento
              </button>

              {customDB.length===0 ? (
                <div style={{textAlign:"center",padding:"40px 20px",color:"#bbb"}}>
                  <div style={{fontSize:40,marginBottom:12}}>🥗</div>
                  <div style={{fontSize:14}}>Aún no tienes alimentos personalizados.</div>
                  <div style={{fontSize:12,marginTop:4}}>Agrega comidas específicas de tu dieta.</div>
                </div>
              ) : (
                customDB.map(f=>(
                  <div key={f.id} style={{background:"#fff",borderRadius:12,padding:"12px 14px",marginBottom:8,display:"flex",alignItems:"center",gap:12,boxShadow:"0 1px 6px rgba(0,0,0,.06)"}}>
                    <div style={{flex:1}}>
                      <div style={{fontSize:14,fontWeight:600}}>{f.n}</div>
                      <div style={{fontSize:11,color:"#888"}}>{f.cat} · {f.cal} kcal/100{f.u}</div>
                      <div style={{fontSize:11,color:"#bbb"}}>P:{f.pro}g C:{f.car}g G:{f.fat}g</div>
                    </div>
                    <button onClick={()=>deleteCustom(f.id)} style={{background:"#FEF2F2",border:"none",borderRadius:8,padding:"6px 10px",color:"#DC2626",fontSize:13}}>✕</button>
                  </div>
                ))
              )}
            </>
          )}

          {/* Vista: Base de datos completa */}
          {graphView==="mis-base" && (
            <>
              <div style={{position:"relative",marginBottom:14}}>
                <input value={query} onChange={e=>setQuery(e.target.value)}
                  placeholder="🔍 Buscar en base de datos..." style={{fontSize:13}}/>
                {query&&<button onClick={()=>setQuery("")} style={{position:"absolute",right:10,top:"50%",transform:"translateY(-50%)",background:"none",border:"none",fontSize:18,color:"#bbb",cursor:"pointer"}}>×</button>}
              </div>

              {DB.length===0 ? (
                <div style={{textAlign:"center",color:"#bbb"}}>No hay alimentos en la base de datos</div>
              ) : (
                DB
                  .filter(f => !query || f.n.toLowerCase().includes(query.toLowerCase()) || f.cat.toLowerCase().includes(query.toLowerCase()))
                  .map(f=>(
                    <div key={f.id} style={{background:"#fff",borderRadius:12,padding:"12px 14px",marginBottom:8,boxShadow:"0 1px 6px rgba(0,0,0,.06)"}}>
                      <div style={{display:"flex",justifyContent:"space-between",alignItems:"start",gap:8}}>
                        <div style={{flex:1}}>
                          <div style={{fontSize:14,fontWeight:600}}>{f.n}</div>
                          <div style={{fontSize:11,color:"#888"}}>{f.cat}</div>
                        </div>
                        <div style={{textAlign:"right",fontSize:11}}>
                          <div style={{fontSize:13,fontWeight:700,color:GREEN}}>{f.cal}k</div>
                          <div style={{color:"#888",fontSize:10}}>kcal/{f.base?`1${f.u}`:`100${f.u}`}</div>
                        </div>
                      </div>
                      <div style={{fontSize:10,color:"#bbb",marginTop:6}}>P:{f.pro}g | C:{f.car}g | G:{f.fat}g</div>
                      {f.custom && <div style={{fontSize:9,color:GREEN,marginTop:3}}>⭐ Alimento personalizado</div>}
                    </div>
                  ))
              )}
            </>
          )}
        </div>
      )}

      {/* ══════ TAB CRONÓMETRO ══════ */}
      {tab==="timer" && (
        <div style={{padding:14}}>
          <div style={{marginBottom:20}}>
            <div style={{fontSize:13,fontWeight:700,color:"#555",marginBottom:20}}>Cronómetro de Entrenamiento</div>
            
            {/* Pantalla del tiempo */}
            <div style={{background:"#fff",borderRadius:14,padding:40,boxShadow:"0 2px 10px rgba(0,0,0,.06)",marginBottom:24,textAlign:"center",border:"1px solid #f0f0f0"}}>
              <div style={{fontSize:64,fontWeight:300,color:GREEN,fontFamily:"'Courier New', monospace",letterSpacing:1,marginBottom:8,lineHeight:1}}>
                {String(Math.floor(timerSeconds/3600)).padStart(2,'0')}:{String(Math.floor((timerSeconds%3600)/60)).padStart(2,'0')}:{String(timerSeconds%60).padStart(2,'0')}
              </div>
              <div style={{fontSize:11,color:"#aaa",letterSpacing:0.5,textTransform:"uppercase"}}>Horas · Minutos · Segundos</div>
            </div>

            {/* Botones de control */}
            <div style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr",gap:10,marginBottom:20}}>
              <button onClick={()=>setTimerRunning(!timerRunning)} 
                style={{padding:13,background:timerRunning?"#DC2626":"#2D9C6B",color:"#fff",border:"none",borderRadius:10,fontWeight:600,fontSize:12,cursor:"pointer",transition:"background 0.2s"}}>
                {timerRunning?"Pausar":"Iniciar"}
              </button>
              <button onClick={()=>{setTimerSeconds(0);setTimerRunning(false);}}
                style={{padding:13,background:"#6B7280",color:"#fff",border:"none",borderRadius:10,fontWeight:600,fontSize:12,cursor:"pointer",transition:"background 0.2s"}}>
                Reiniciar
              </button>
              <button onClick={()=>{
                if(timerSeconds>0) {
                  alert(`Sesión guardada\\n${String(Math.floor(timerSeconds/3600)).padStart(2,'0')}:${String(Math.floor((timerSeconds%3600)/60)).padStart(2,'0')}:${String(timerSeconds%60).padStart(2,'0')}`);
                  setTimerSeconds(0);
                  setTimerRunning(false);
                }
              }}
                style={{padding:13,background:"#3B82F6",color:"#fff",border:"none",borderRadius:10,fontWeight:600,fontSize:12,cursor:"pointer",transition:"background 0.2s"}}>
                Guardar
              </button>
            </div>

            {/* Stats */}
            <div style={{background:"#f9fafb",borderRadius:12,padding:14,marginBottom:14,border:"1px solid #e5e7eb"}}>
              <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:12}}>
                <div>
                  <div style={{fontSize:10,color:"#6B7280",textTransform:"uppercase",letterSpacing:0.5,marginBottom:4}}>Tiempo Total</div>
                  <div style={{fontSize:18,fontWeight:600,color:GREEN}}>{String(Math.floor(timerSeconds/3600)).padStart(2,'0')}:{String(Math.floor((timerSeconds%3600)/60)).padStart(2,'0')}</div>
                </div>
                <div>
                  <div style={{fontSize:10,color:"#6B7280",textTransform:"uppercase",letterSpacing:0.5,marginBottom:4}}>Segundos</div>
                  <div style={{fontSize:18,fontWeight:600,color:"#3B82F6"}}>{String(timerSeconds%60).padStart(2,'0')}</div>
                </div>
              </div>
            </div>

            {/* Instrucciones */}
            <div style={{background:"#f0f9f7",borderRadius:10,padding:12,fontSize:11,color:"#555",lineHeight:1.6,border:"1px solid #d1e8e4"}}>
              <div style={{fontWeight:600,marginBottom:6,color:"#2D9C6B"}}>Instrucciones</div>
              <div>
                • Presiona <strong>Iniciar</strong> para comenzar tu entrenamiento<br/>
                • Presiona <strong>Pausar</strong> para detener el tiempo<br/>
                • Presiona <strong>Guardar</strong> para registrar la sesión
              </div>
            </div>
          </div>
        </div>
      )}

      {/* ══════ MODAL AGREGAR COMIDA ══════ */}
      {modal==="add" && (
        <div onClick={()=>{setModal(null);setQuery("");setSelFood(null);}} style={{position:"fixed",inset:0,background:"rgba(0,0,0,.45)",display:"flex",alignItems:"flex-end",justifyContent:"center",zIndex:1000}}>
          <div onClick={e=>e.stopPropagation()} style={{background:"#fff",borderRadius:"20px 20px 0 0",padding:20,width:"100%",maxWidth:480,maxHeight:"88vh",display:"flex",flexDirection:"column",gap:0}}>
            <div style={{fontSize:15,fontWeight:700,marginBottom:4}}>Agregar alimento</div>
            <div style={{fontSize:12,color:"#888",marginBottom:10}}>Comida: <strong style={{color:GREEN}}>{meal}</strong></div>

            {/* Selector comida */}
            <div style={{display:"flex",gap:5,marginBottom:12,overflowX:"auto",paddingBottom:2}}>
              {MEALS.map(m=>(
                <button key={m} onClick={()=>setMeal(m)} style={{
                  background:meal===m?GREEN:"#f5f5f5",color:meal===m?"#fff":"#666",
                  border:"none",borderRadius:99,padding:"5px 12px",fontSize:12,whiteSpace:"nowrap",flexShrink:0
                }}>{m}</button>
              ))}
            </div>

            {/* Buscador */}
            <div style={{position:"relative",marginBottom:8}}>
              <input ref={searchRef} value={query} onChange={e=>{setQuery(e.target.value);setSelFood(null);}}
                placeholder="Buscar alimento... (ej: pollo, arroz, manzana)"/>
              {query&&<button onClick={()=>{setQuery("");setSelFood(null);}} style={{position:"absolute",right:10,top:"50%",transform:"translateY(-50%)",background:"none",border:"none",fontSize:18,color:"#bbb"}}>×</button>}
            </div>

            {/* Resultados */}
            {!selFood && filtered.length>0 && (
              <div style={{flex:1,overflowY:"auto",border:"1px solid #eee",borderRadius:10,marginBottom:10}}>
                {filtered.map(f=>(
                  <div key={f.id} onClick={()=>selectFood(f)} style={{padding:"9px 12px",borderBottom:"1px solid #f8f8f8",cursor:"pointer",display:"flex",justifyContent:"space-between",alignItems:"center"}}>
                    <div>
                      <div style={{fontSize:13}}>{f.n}{f.custom&&<span style={{fontSize:10,background:LGREEN,color:GREEN,borderRadius:4,padding:"1px 5px",marginLeft:5}}>mío</span>}</div>
                      <div style={{fontSize:10,color:"#bbb"}}>{f.cat} · {f.cal} kcal/{f.base?`1 ${f.u}`:`100${f.u}`}</div>
                    </div>
                    <div style={{fontWeight:700,color:GREEN}}>{f.cal}</div>
                  </div>
                ))}
              </div>
            )}
            {!selFood && query.length>1 && filtered.length===0 && (
              <div style={{textAlign:"center",padding:"16px 0",color:"#bbb",fontSize:13}}>
                Sin resultados · <button onClick={()=>setModal("nuevo")} style={{background:"none",border:"none",color:GREEN,fontWeight:600,fontSize:13,cursor:"pointer"}}>Crear "{query}"</button>
              </div>
            )}

            {/* Alimento seleccionado */}
            {selFood && (
              <div style={{marginBottom:10}}>
                <div style={{background:LGREEN,borderRadius:10,padding:"10px 14px",marginBottom:10}}>
                  <div style={{fontSize:13,fontWeight:600}}>{selFood.n}</div>
                  <div style={{fontSize:11,color:"#888"}}>{selFood.cat}</div>
                </div>
                <label style={{fontSize:12,color:"#666",display:"block",marginBottom:4}}>
                  Cantidad ({selFood.u==="un"?"unidades":selFood.u==="pl"?"g (porción)":selFood.u})
                </label>
                <input type="number" value={amount} min={1} step={selFood.u==="un"?1:5}
                  onChange={e=>setAmount(Number(e.target.value))} style={{textAlign:"center",fontSize:20,fontWeight:700,marginBottom:8}}/>
                {(()=>{
                  const p=calcEntry(selFood,amount);
                  return (
                    <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",gap:4,textAlign:"center",background:"#f9f9f9",borderRadius:10,padding:10}}>
                      {[["Calorías",p.cal,"kcal",GREEN],["Prot.",p.pro,"g","#3B82F6"],["Carbs",p.car,"g","#F97316"],["Grasas",p.fat,"g","#8B5CF6"]].map(([l,v,u,c])=>(
                        <div key={l}><div style={{fontSize:16,fontWeight:700,color:c}}>{v}</div><div style={{fontSize:9,color:"#aaa"}}>{l}</div></div>
                      ))}
                    </div>
                  );
                })()}
              </div>
            )}

            <div style={{display:"flex",gap:4,marginTop:8}}>
              <button onClick={()=>setModal("nuevo")} style={{flex:1,padding:11,background:"#f0fdf4",color:GREEN,border:`1px solid ${GREEN}`,borderRadius:10,fontSize:13,fontWeight:600}}>+ Crear alimento</button>
              <button onClick={addEntry} disabled={!selFood} style={{flex:2,padding:11,background:selFood?GREEN:"#ccc",color:"#fff",border:"none",borderRadius:10,fontSize:14,fontWeight:700}}>Agregar</button>
            </div>
          </div>
        </div>
      )}

      {/* ══════ MODAL NUEVO ALIMENTO ══════ */}
      {modal==="nuevo" && (
        <div onClick={()=>setModal("add")} style={{position:"fixed",inset:0,background:"rgba(0,0,0,.45)",display:"flex",alignItems:"flex-end",justifyContent:"center",zIndex:1100}}>
          <div onClick={e=>e.stopPropagation()} style={{background:"#fff",borderRadius:"20px 20px 0 0",padding:20,width:"100%",maxWidth:480,maxHeight:"90vh",overflowY:"auto"}}>
            <div style={{fontSize:15,fontWeight:700,marginBottom:4}}>Crear alimento personalizado</div>
            <div style={{fontSize:12,color:"#888",marginBottom:14}}>Los valores son por 100g o por unidad según corresponda.</div>

            <div style={{marginBottom:10}}>
              <label style={{fontSize:12,color:"#666",display:"block",marginBottom:3}}>Nombre *</label>
              <input value={newFood.n} onChange={e=>setNewFood(f=>({...f,n:e.target.value}))} placeholder="Ej: Wrap de pollo casero"/>
            </div>

            <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8,marginBottom:10}}>
              <div>
                <label style={{fontSize:12,color:"#666",display:"block",marginBottom:3}}>Calorías (kcal) *</label>
                <input type="number" value={newFood.cal} onChange={e=>setNewFood(f=>({...f,cal:e.target.value}))} placeholder="ej: 250" style={{textAlign:"center"}}/>
              </div>
              <div>
                <label style={{fontSize:12,color:"#666",display:"block",marginBottom:3}}>Unidad</label>
                <select value={newFood.u} onChange={e=>setNewFood(f=>({...f,u:e.target.value}))}>
                  <option value="g">gramos (g)</option>
                  <option value="ml">mililitros (ml)</option>
                  <option value="un">unidades (un)</option>
                  <option value="pl">porción (pl)</option>
                </select>
              </div>
            </div>

            <div style={{display:"grid",gridTemplateColumns:"repeat(3,1fr)",gap:8,marginBottom:10}}>
              <div>
                <label style={{fontSize:12,color:"#666",display:"block",marginBottom:3}}>Proteínas (g)</label>
                <input type="number" value={newFood.pro} onChange={e=>setNewFood(f=>({...f,pro:e.target.value}))} placeholder="0" style={{textAlign:"center"}}/>
              </div>
              <div>
                <label style={{fontSize:12,color:"#666",display:"block",marginBottom:3}}>Carbos (g)</label>
                <input type="number" value={newFood.car} onChange={e=>setNewFood(f=>({...f,car:e.target.value}))} placeholder="0" style={{textAlign:"center"}}/>
              </div>
              <div>
                <label style={{fontSize:12,color:"#666",display:"block",marginBottom:3}}>Grasas (g)</label>
                <input type="number" value={newFood.fat} onChange={e=>setNewFood(f=>({...f,fat:e.target.value}))} placeholder="0" style={{textAlign:"center"}}/>
              </div>
            </div>

            <div style={{marginBottom:16}}>
              <label style={{fontSize:12,color:"#666",display:"block",marginBottom:3}}>Categoría</label>
              <select value={newFood.cat} onChange={e=>setNewFood(f=>({...f,cat:e.target.value}))}>
                <option value="⭐ Mis alimentos">⭐ Mis alimentos</option>
                <option value="🥩 Proteínas">🥩 Proteínas</option>
                <option value="🌾 Cereales">🌾 Cereales</option>
                <option value="🥦 Verduras">🥦 Verduras</option>
                <option value="🍎 Frutas">🍎 Frutas</option>
                <option value="🇨🇱 Cocina chilena">🇨🇱 Cocina chilena</option>
                <option value="🥤 Bebidas">🥤 Bebidas</option>
                <option value="🍫 Snacks">🍫 Snacks</option>
              </select>
            </div>

            <div style={{display:"flex",gap:8}}>
              <button onClick={()=>setModal("add")} style={{flex:1,padding:12,background:"#f5f5f5",color:"#555",border:"none",borderRadius:10,fontSize:13}}>Cancelar</button>
              <button onClick={saveNewFood} style={{flex:2,padding:12,background:GREEN,color:"#fff",border:"none",borderRadius:10,fontSize:14,fontWeight:700}}>Guardar alimento</button>
            </div>
          </div>
        </div>
      )}

      {/* ══════ MODAL META ══════ */}
      {modal==="goal" && (
        <div onClick={()=>setModal(null)} style={{position:"fixed",inset:0,background:"rgba(0,0,0,.45)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:1000,padding:20}}>
          <div onClick={e=>e.stopPropagation()} style={{background:"#fff",borderRadius:16,padding:20,width:"100%",maxWidth:400,maxHeight:"90vh",overflowY:"auto"}}>
            <div style={{fontSize:15,fontWeight:700,marginBottom:14}}>Configurar metas personalizadas</div>

            {/* ── Perfil de usuario ── */}
            <div style={{background:"#f9f9f9",borderRadius:12,padding:14,marginBottom:14}}>
              <div style={{fontSize:13,fontWeight:600,color:"#333",marginBottom:10}}>Tus datos</div>
              
              <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8,marginBottom:8}}>
                <div>
                  <label style={{fontSize:11,color:"#666",display:"block",marginBottom:4}}>Peso (kg)</label>
                  <input type="number" value={userProfile.weight} onChange={e=>{const p={...userProfile,weight:Number(e.target.value)}; setUserProfile(p); localStorage.setItem("cal_profile",JSON.stringify(p)); recalculateGoal(p);}} style={{textAlign:"center",fontSize:14}}/>
                </div>
                <div>
                  <label style={{fontSize:11,color:"#666",display:"block",marginBottom:4}}>Altura (cm)</label>
                  <input type="number" value={userProfile.height} onChange={e=>{const p={...userProfile,height:Number(e.target.value)}; setUserProfile(p); localStorage.setItem("cal_profile",JSON.stringify(p)); recalculateGoal(p);}} style={{textAlign:"center",fontSize:14}}/>
                </div>
              </div>

              <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8,marginBottom:8}}>
                <div>
                  <label style={{fontSize:11,color:"#666",display:"block",marginBottom:4}}>Edad</label>
                  <input type="number" value={userProfile.age} onChange={e=>{const p={...userProfile,age:Number(e.target.value)}; setUserProfile(p); localStorage.setItem("cal_profile",JSON.stringify(p)); recalculateGoal(p);}} style={{textAlign:"center",fontSize:14}}/>
                </div>
                <div>
                  <label style={{fontSize:11,color:"#666",display:"block",marginBottom:4}}>Sexo</label>
                  <select value={userProfile.sex} onChange={e=>{const p={...userProfile,sex:e.target.value}; setUserProfile(p); localStorage.setItem("cal_profile",JSON.stringify(p)); recalculateGoal(p);}} style={{fontSize:14}}>
                    <option value="M">Masculino</option>
                    <option value="F">Femenino</option>
                  </select>
                </div>
              </div>

              <div style={{marginBottom:8}}>
                <label style={{fontSize:11,color:"#666",display:"block",marginBottom:4}}>Actividad física</label>
                <select value={userProfile.activityLevel} onChange={e=>{const p={...userProfile,activityLevel:Number(e.target.value)}; setUserProfile(p); localStorage.setItem("cal_profile",JSON.stringify(p)); recalculateGoal(p);}} style={{fontSize:14}}>
                  <option value={1.2}>Sedentario</option>
                  <option value={1.375}>Poco activo</option>
                  <option value={1.5}>Moderadamente activo</option>
                  <option value={1.725}>Muy activo</option>
                  <option value={1.9}>Extremadamente activo</option>
                </select>
              </div>

              <div>
                <label style={{fontSize:11,color:"#666",display:"block",marginBottom:4}}>Objetivo</label>
                <select value={userProfile.objective} onChange={e=>{const p={...userProfile,objective:e.target.value}; setUserProfile(p); localStorage.setItem("cal_profile",JSON.stringify(p)); recalculateGoal(p);}} style={{fontSize:14}}>
                  <option value="weightLoss">Pérdida de peso</option>
                  <option value="maintenance">Mantenimiento</option>
                  <option value="muscleGain">Ganancia muscular</option>
                </select>
              </div>
            </div>

            {/* ── Meta calculada automáticamente ── */}
            <div style={{marginBottom:14}}>
              <label style={{fontSize:12,color:"#666",display:"block",marginBottom:6}}>Tu meta de calorías diaria</label>
              <input type="number" value={goal} step={50} min={1000} max={5000}
                onChange={e=>{setGoal(Number(e.target.value));localStorage.setItem("cal_goal",e.target.value);}}
                style={{textAlign:"center",fontSize:20,fontWeight:700,color:GREEN,marginBottom:8}}/>
              <div style={{fontSize:11,color:"#888"}}>Calculada automáticamente según tu perfil (Mifflin-St Jeor)</div>
            </div>

            {/* ── Meta de agua ── */}
            <div style={{marginBottom:16}}>
              <label style={{fontSize:12,color:"#666",display:"block",marginBottom:6}}>Meta de agua (vasos de 250ml)</label>
              <div style={{display:"flex",alignItems:"center",gap:10}}>
                <button onClick={()=>{const v=Math.max(4,waterGoal-1);setWaterGoal(v);localStorage.setItem("cal_wgoal",v);}}
                  style={{width:36,height:36,background:"#f5f5f5",border:"none",borderRadius:8,fontSize:20}}>−</button>
                <div style={{flex:1,textAlign:"center",fontSize:20,fontWeight:700,color:BLUE}}>{waterGoal} <span style={{fontSize:13,color:"#aaa",fontWeight:400}}>vasos · {waterGoal*250}ml</span></div>
                <button onClick={()=>{const v=Math.min(16,waterGoal+1);setWaterGoal(v);localStorage.setItem("cal_wgoal",v);}}
                  style={{width:36,height:36,background:LBLUE,border:"none",borderRadius:8,fontSize:20}}>+</button>
              </div>
            </div>

            <button onClick={()=>setModal(null)} style={{width:"100%",padding:12,background:GREEN,color:"#fff",border:"none",borderRadius:10,fontWeight:700,fontSize:14}}>Listo</button>
          </div>
        </div>
      )}

      {/* ══════ MODAL DETALLE ENTRADA ══════ */}
      {modal==="detail" && detEntry && (
        <div onClick={()=>setModal(null)} style={{position:"fixed",inset:0,background:"rgba(0,0,0,.45)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:1000,padding:20}}>
          <div onClick={e=>e.stopPropagation()} style={{background:"#fff",borderRadius:16,padding:20,width:"100%",maxWidth:320}}>
            <div style={{fontSize:15,fontWeight:700,marginBottom:4}}>{detEntry.name}</div>
            <div style={{fontSize:12,color:"#888",marginBottom:14}}>{detEntry.meal} · {detEntry.amount}{detEntry.unit}</div>
            <div style={{display:"grid",gridTemplateColumns:"repeat(2,1fr)",gap:8,marginBottom:16}}>
              {[["Calorías",Math.round(detEntry.cal),"kcal",GREEN],["Proteínas",detEntry.pro,"g","#3B82F6"],["Carbohidratos",detEntry.car,"g","#F97316"],["Grasas",detEntry.fat,"g","#8B5CF6"]].map(([l,v,u,c])=>(
                <div key={l} style={{background:"#f9f9f9",borderRadius:10,padding:"10px 12px",textAlign:"center"}}>
                  <div style={{fontSize:20,fontWeight:700,color:c}}>{v}</div>
                  <div style={{fontSize:11,color:"#aaa"}}>{l}</div>
                </div>
              ))}
            </div>
            <div style={{display:"flex",gap:8}}>
              <button onClick={()=>removeEntry(detEntry.id)} style={{flex:1,padding:11,background:"#FEF2F2",color:"#DC2626",border:"1px solid #fca5a5",borderRadius:10,fontSize:13,fontWeight:600}}>Eliminar</button>
              <button onClick={()=>setModal(null)} style={{flex:1,padding:11,background:"#f5f5f5",color:"#555",border:"none",borderRadius:10,fontSize:13}}>Cerrar</button>
            </div>
          </div>
        </div>
      )}

      {/* ══════ MODAL FEEDBACK ══════ */}
      {modal==="feedback" && (
        <div onClick={()=>setModal(null)} style={{position:"fixed",inset:0,background:"rgba(0,0,0,.45)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:1000,padding:20}}>
          <div onClick={e=>e.stopPropagation()} style={{background:"#fff",borderRadius:16,padding:20,width:"100%",maxWidth:380}}>
            <div style={{fontSize:15,fontWeight:700,marginBottom:14}}>Enviar comentario</div>
            
            <form action="https://formsubmit.co/Leonardo.recast@gmal.com" method="POST" style={{display:"flex",flexDirection:"column",gap:10}}>
              <textarea name="mensaje" placeholder="Cuéntanos qué te gustaría mejorar o si encontraste algún error..." 
                required style={{width:"100%",minHeight:100,padding:10,borderRadius:10,border:"1px solid #e0e0e0",marginBottom:4,fontSize:13,fontFamily:"inherit",resize:"vertical"}}/>
              
              <input type="email" name="email" placeholder="Tu email (opcional)" 
                style={{width:"100%",padding:9,borderRadius:10,border:"1px solid #e0e0e0",fontSize:13}}/>
              
              <input type="hidden" name="_captcha" value="false"/>
              <input type="hidden" name="_subject" value="Nuevo feedback de NUTRIPEAK"/>
              <input type="hidden" name="_autoresponse" value="Gracias por tu feedback! Lo revisaremos pronto."/>
              
              <div style={{display:"flex",gap:8}}>
                <button type="submit"
                  style={{flex:1,padding:11,background:GREEN,color:"#fff",border:"none",borderRadius:10,fontWeight:700,fontSize:13,cursor:"pointer"}}>Enviar</button>
                <button type="button" onClick={()=>setModal(null)}
                  style={{flex:1,padding:11,background:"#f5f5f5",color:"#555",border:"none",borderRadius:10,fontSize:13,cursor:"pointer"}}>Cancelar</button>
              </div>
            </form>
          </div>
        </div>
      )}

      {/* ══════ MODAL CRÉDITOS ══════ */}
      {modal==="creditos" && (
        <div onClick={()=>setModal(null)} style={{position:"fixed",inset:0,background:"rgba(0,0,0,.45)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:1000,padding:20}}>
          <div onClick={e=>e.stopPropagation()} style={{background:"#fff",borderRadius:16,padding:20,width:"100%",maxWidth:380}}>
            <div style={{textAlign:"center",marginBottom:20}}>
              <div style={{fontSize:28,fontWeight:700,color:GREEN,marginBottom:4}}>NUTRIPEAK</div>
              <div style={{fontSize:12,color:"#888"}}>Control Nutricional Inteligente</div>
            </div>

            <div style={{background:"#f9f9f9",borderRadius:12,padding:14,marginBottom:16,fontSize:13,lineHeight:1.6}}>
              <div style={{fontWeight:700,color:"#333",marginBottom:8}}>📝 Desarrollado por</div>
              <div style={{color:"#666",marginBottom:12}}>
                <strong>Leonardo Rebolledo</strong><br/>
                CEO - RECAST
              </div>

              <div style={{fontWeight:700,color:"#333",marginBottom:8}}>📍 Ubicación</div>
              <div style={{color:"#666",marginBottom:12}}>
                Antofagasta, Chile
              </div>

              <div style={{fontWeight:700,color:"#333",marginBottom:8}}>📅 Año</div>
              <div style={{color:"#666"}}>
                2026
              </div>
            </div>

            <div style={{fontSize:11,color:"#aaa",textAlign:"center",marginBottom:14,lineHeight:1.5}}>
              NUTRIPEAK es una aplicación de control nutricional desarrollada para ayudarte a alcanzar tus objetivos de salud y fitness.
            </div>

            <button onClick={()=>setModal(null)} 
              style={{width:"100%",padding:11,background:GREEN,color:"#fff",border:"none",borderRadius:10,fontWeight:700,fontSize:13,cursor:"pointer"}}>Cerrar</button>
          </div>
        </div>
      )}

    </div>
  );
}

ReactDOM.createRoot(document.getElementById("root")).render(<App/>);
</script>
</body>
</html>
