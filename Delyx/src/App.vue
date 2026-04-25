<template>
  <header>
    <h1>Delyx</h1>
    <div class="search-bar">
      🔍 Buscar plato o bebida...
    </div>
  </header>
  <main>
    <div class="categories">
      <div class="cat-chip active">Todo</div>
      <div class="cat-chip">Entradas</div>
      <div class="cat-chip">Fuertes</div>
      <div class="cat-chip">Cocteles</div>
      <div class="cat-chip">Vinos</div>
    </div>
    <div class="content">
      <div class="items-grid">
        <div class="item-card" v-for="item in items" :key="item.name">
          <img :src="item.image" :alt="item.name" class="item-thumb" />
          <span class="item-name">{{ item.name }}</span>
          <span class="item-desc">{{ item.descripcion }}</span>
          <span class="item-price">${{ item.price }}</span>
        </div>
      </div>

      <div class="cuenta">
        <div class="cart-summary">
          <div class="titulo">
            <h1>Cuenta</h1>
          </div>
          <div class="titulos_P_C">
            <h2>Producto</h2>
            <h2>Cantidad</h2>
          </div>
          <div class="grey-bar"></div>
          <!-- ESTO ES EN JS -->
          <div class="Producto_Cantidad">
            <div class="producto">
              <div id="nombre_producto"> holsad</div>
            </div>
            <div class="Cantidad_Precio">
              <div class="cantidad">
                <div id="cantidad_producto"> <button class="boton_cantidad">-</button><span
                    class="cuadro_Cantidad">1</span><button class="boton_cantidad">+</button></div>
              </div>
              <div class="precio">
                <div id="precio_producto">x $ 10.000 = <span style="font-weight: bold;">$ 10.000</span> </div>
              </div>
            </div>
          </div>

          <!-- HASTA ACÁ -->
          <div class="grey-bar"></div>
        </div>
        <button class="checkout-btn">Finalizar Pedido</button>
      </div>
    </div>
  </main>


</template>

<script setup>
import { ref } from 'vue'

const items = ref([
  // ENTRADAS (10)
  {
    name: 'Cóctel de Camarón',
    price: 28000,
    image: 'https://images.unsplash.com/photo-1565680018434-b513d5e5fd47?w=400&q=80',
    category: 'entradas',
    descripcion: 'Camarones frescos en salsa de tomate con aguacate y limón.',
    stock: 12
  },
  {
    name: 'Ostras Frescas',
    price: 35000,
    image: 'https://lalangosta.com.co/wp-content/uploads/2023/12/ostras-frescas-e1744915406992.jpg',
    category: 'entradas',
    descripcion: 'Ostras del día servidas en media concha con vinagreta de chalota.',
    stock: 8
  },
  {
    name: 'Ceviche Clásico',
    price: 30000,
    image: 'https://images.unsplash.com/photo-1535399831218-d5bd36d1a6b3?w=400&q=80',
    category: 'entradas',
    descripcion: 'Pescado blanco marinado en limón con cilantro, cebolla y ají.',
    stock: 15
  },
  {
    name: 'Tempura de Camarón',
    price: 26000,
    image: 'https://png.pngtree.com/background/20250203/original/pngtree-crispy-shrimp-tempura-picture-image_15832804.jpg',
    category: 'entradas',
    descripcion: 'Camarones en masa crujiente de tempura con salsa ponzu.',
    stock: 10
  },
  {
    name: 'Ensalada de Mariscos',
    price: 24000,
    image: 'https://images.unsplash.com/photo-1512621776951-a57141f2eefd?w=400&q=80',
    category: 'entradas',
    descripcion: 'Mix de mariscos sobre hojas verdes con aderezo de mostaza y miel.',
    stock: 9
  },
  {
    name: 'Pan con Ajo',
    price: 12000,
    image: 'https://images.unsplash.com/photo-1573140401552-3fab0b24306f?w=400&q=80',
    category: 'entradas',
    descripcion: 'Pan artesanal tostado con mantequilla de ajo y perejil fresco.',
    stock: 20
  },
  {
    name: 'Tabla de Quesos',
    price: 20000,
    image: 'https://images.unsplash.com/photo-1452195100486-9cc805987862?w=400&q=80',
    category: 'entradas',
    descripcion: 'Selección de quesos artesanales con miel, nueces y mermelada.',
    stock: 7
  },
  {
    name: 'Dumplings',
    price: 18000,
    image: 'https://images.unsplash.com/photo-1563245372-f21724e3856d?w=400&q=80',
    category: 'entradas',
    descripcion: 'Dumplings rellenos de cerdo y jengibre al vapor, con salsa de soya.',
    stock: 14
  },
  {
    name: 'Sopa de Mariscos',
    price: 22000,
    image: 'https://images.unsplash.com/photo-1547592166-23ac45744acd?w=400&q=80',
    category: 'entradas',
    descripcion: 'Caldo concentrado de mariscos con almejas, mejillones y hierbas.',
    stock: 11
  },
  {
    name: 'Bruschetta',
    price: 15000,
    image: 'https://images.unsplash.com/photo-1572695157366-5e585ab2b69f?w=400&q=80',
    category: 'entradas',
    descripcion: 'Pan tostado con tomate cherry, albahaca fresca y aceite de oliva.',
    stock: 18
  },

  // FUERTES (10)
  {
    name: 'Langosta a la Parrilla',
    price: 65000,
    image: 'https://adamsfarms.com/wp-content/uploads/2021/05/Grilled-Lobster-Tails.jpg',
    category: 'fuertes',
    descripcion: 'Langosta entera a la parrilla con mantequilla de limón y hierbas.',
    stock: 4
  },
  {
    name: 'Salmón al Grill',
    price: 48000,
    image: 'https://images.unsplash.com/photo-1519708227418-c8fd9a32b7a2?w=400&q=80',
    category: 'fuertes',
    descripcion: 'Filete de salmón atlántico con costra de mostaza y puré de papa.',
    stock: 10
  },
  {
    name: 'Pasta Marinera',
    price: 42000,
    image: 'https://images.unsplash.com/photo-1563379091339-03b21ab4a4f8?w=400&q=80',
    category: 'fuertes',
    descripcion: 'Linguine con camarones, mejillones y almejas en salsa de tomate.',
    stock: 13
  },
  {
    name: 'Arroz con Mariscos',
    price: 40000,
    image: 'https://images.unsplash.com/photo-1534422298391-e4f8c172dddb?w=400&q=80',
    category: 'fuertes',
    descripcion: 'Arroz cremoso con mix de mariscos frescos y azafrán.',
    stock: 8
  },
  {
    name: 'Berenjena Asada',
    price: 25000,
    image: 'https://veganwetdreams.com/wp-content/uploads/2023/09/Berenjenas-al-horno.jpg',
    category: 'fuertes',
    descripcion: 'Berenjena al horno con salsa de yogur, granada y menta.',
    stock: 9
  },
  {
    name: 'Bowl Vegetariano',
    price: 27000,
    image: 'https://images.unsplash.com/photo-1546069901-ba9599a7e63c?w=400&q=80',
    category: 'fuertes',
    descripcion: 'Bowl de quinoa con vegetales asados, aguacate y aderezo tahini.',
    stock: 12
  },
  {
    name: 'Pollo BBQ',
    price: 32000,
    image: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR7zk5gTAmSXbds6gqv5RnI2iCromzk933DNQ&s',
    category: 'fuertes',
    descripcion: 'Pechuga de pollo ahumada con salsa BBQ casera y papas rústicas.',
    stock: 15
  },
  {
    name: 'Baby Beef',
    price: 52000,
    image: 'https://images.unsplash.com/photo-1558030006-450675393462?w=400&q=80',
    category: 'fuertes',
    descripcion: 'Lomo fino de res a la parrilla, término al gusto, con chimichurri.',
    stock: 6
  },
  {
    name: 'Pizza Margarita',
    price: 30000,
    image: 'https://images.unsplash.com/photo-1574071318508-1cdbab80d002?w=400&q=80',
    category: 'fuertes',
    descripcion: 'Pizza de masa fina con salsa de tomate, mozzarella y albahaca.',
    stock: 16
  },
  {
    name: 'Tacos de Pescado',
    price: 29000,
    image: 'https://images.unsplash.com/photo-1565299585323-38d6b0865b47?w=400&q=80',
    category: 'fuertes',
    descripcion: 'Tortillas de maíz con pescado empanizado, col y crema de chipotle.',
    stock: 11
  },

  // COCTELES (5)
  {
    name: 'Margarita',
    price: 22000,
    image: 'https://assets.tmecosys.com/image/upload/t_web_rdp_recipe_584x480_1_5x/img/recipe/ras/Assets/e75955d6ec97e3138ed8e3b660e3e06d/Derivates/5e4e3c19376adc8b65408b91e0d0f7ed0edadaf8.jpg',
    category: 'cocteles',
    descripcion: 'Tequila, triple sec y jugo de limón con borde de sal.',
    stock: 20
  },
  {
    name: 'Martini',
    price: 24000,
    image: 'https://images.unsplash.com/photo-1560508180-03f285f67ded?w=400&q=80',
    category: 'cocteles',
    descripcion: 'Ginebra y vermú seco agitados, servidos con aceituna.',
    stock: 20
  },
  {
    name: 'Daiquiri Fresa',
    price: 20000,
    image: 'https://images.unsplash.com/photo-1497534446932-c925b458314e?w=400&q=80',
    category: 'cocteles',
    descripcion: 'Ron blanco, fresas frescas y limón batidos con hielo.',
    stock: 20
  },
  {
    name: 'Piña Colada',
    price: 23000,
    image: 'https://cdn7.kiwilimon.com/recetaimagen/41495/960x640/56676.jpg.jpg',
    category: 'cocteles',
    descripcion: 'Ron, crema de coco y jugo de piña con piña fresca de guarnición.',
    stock: 20
  },
  {
    name: 'Mojito',
    price: 21000,
    image: 'https://images.unsplash.com/photo-1551538827-9c037cb4f32a?w=400&q=80',
    category: 'cocteles',
    descripcion: 'Ron blanco, menta fresca, limón y soda bien fría.',
    stock: 20
  },

  // VINOS (5)
  {
    name: 'Vino Tinto',
    price: 18000,
    image: 'https://images.unsplash.com/photo-1510812431401-41d2bd2722f3?w=400&q=80',
    category: 'vinos',
    descripcion: 'Copa de vino tinto de la casa, suave y afrutado. Maridaje ideal con carnes.',
    stock: 30
  },
  {
    name: 'Vino Blanco',
    price: 18000,
    image: 'https://tienda.cristar.com.co/cdn/shop/articles/COPAS-PARA-VINO-BLANCO-1_de37715d-cedf-46b1-9f95-e23235b2085d.jpg?v=1753908276&width=1600',
    category: 'vinos',
    descripcion: 'Copa de vino blanco seco y fresco. Ideal con pescados y mariscos.',
    stock: 30
  },
  {
    name: 'Rosé',
    price: 19000,
    image: 'https://images.unsplash.com/photo-1547595628-c61a29f496f0?w=400&q=80',
    category: 'vinos',
    descripcion: 'Vino rosado ligero y floral, perfecto para ocasiones especiales.',
    stock: 25
  },
  {
    name: 'Champagne',
    price: 45000,
    image: 'https://images.unsplash.com/photo-1578911373434-0cb395d2cbfb?w=400&q=80',
    category: 'vinos',
    descripcion: 'Copa de champagne brut para celebrar con estilo.',
    stock: 15
  },
  {
    name: 'Malbec Reserva',
    price: 32000,
    image: 'https://images.unsplash.com/photo-1474722883778-792e7990302f?w=400&q=80',
    category: 'vinos',
    descripcion: 'Malbec argentino reserva, robusto con notas de ciruela y tabaco.',
    stock: 20
  }
])
</script>

<style>
/* RESET */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Inter', sans-serif;
}

/* BODY */
body {
  background: #0f0f14;
  color: #fff;
}

/* HEADER */
header {
  padding: 20px;
  background: #15151d;
  position: sticky;
  top: 0;
  z-index: 10;
}

header h1 {
  font-family: 'Poppins', sans-serif;
  font-size: 28px;
  /* Tamaño ideal para destacar */
  font-weight: 700;
  letter-spacing: -0.5px;
  color: #ff4d6d;
}

.cuadro_Cantidad {
  background: #414157;
  color: white;
  padding: 5px 20px
}

.boton_cantidad {
  background: #ff4d6d;
  border: none;
  padding: 5px 10px;
  color: white;
  cursor: pointer;
  transition: 0.2s;
  font-size: 16px;
  cursor: pointer
}

.boton_cantidad:hover {
  background: #ff6b86;
}

.Cantidad_Precio {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.Producto_Cantidad {
  display: flex;
  padding: 20px;
  justify-content: space-between;
  width: 100%;
  font-size: 16px;
}

.titulos_P_C {
  display: flex;
  justify-content: space-between;
  padding: 0 20px;

}

.grey-bar {
  width: 100%;
  height: 0.2vh;
  background: #343447;
  margin: 10px 0;
}

.titulo {
  display: flex;
  flex-direction: column;
  padding: 20px;
  align-items: center;
}


.content {
  display: flex;
  flex-direction: row;
  gap: 20px;
  padding: 2%;

}

.cuenta {
  background: #1f1f2b;
  padding: 15px;
  border-radius: 12px;
  width: 30%;
  display: flex;
  flex-direction: column;
  gap: 20px;
  border-top: 5px solid #ff4d6d;
  position: sticky;
  top: 100px;
  height: fit-content;
  box-shadow: 0 4px 20px rgba(255, 77, 109, 0.15);
}

.titulo h1 {
  font-size: 22px;
  font-weight: 600;
}

.search-bar {
  background: #1f1f2b;
  padding: 10px;
  border-radius: 12px;
  color: #aaa;
}

/* CATEGORIES */
.categories {
  display: flex;
  gap: 10px;
  padding: 15px;
  overflow-x: auto;
}

.cat-chip {
  padding: 8px 14px;
  background: #1f1f2b;
  border-radius: 999px;
  font-size: 14px;
  cursor: pointer;
  white-space: nowrap;
  transition: 0.2s;
}

.cat-chip:hover {
  background: #2a2a3a;
}

.cat-chip.active {
  background: #ff4d6d;
  color: white;
}

/* GRID */
.items-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 15px;
  padding: 15px;
  width: 70%;
  overflow-x: auto;
  min-width: 800px;
}

/* CARD */
.item-card {
  display: flex;
  background: #1a1a25;
  border-radius: 18px;
  padding: 15px;
  position: relative;
  transition: 0.25s;
  cursor: pointer;
  padding: 10px;
  min-width: 0;
  flex-direction: column;
justify-content: center;
align-items: center;
gap: 20px
}

.item-card:hover {
  transform: translateY(-5px);
  background: #222233;
}


.item-thumb {
  width: 100%;
  height: 120px;
  object-fit: cover;
  border-radius: 10px;
  margin-bottom: 10px;
}

/* TEXT */
.item-name {
  display: block;
  font-size: 14px;
  /* Un pelín más grande para que sea legible */
  font-weight: 600;
  margin-bottom: 5px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.item-price {
  font-size: 13px;
  font-weight: 400;
  color: #8e8e93;
  /* Un gris más suave para jerarquía visual */
}

/* ADD BUTTON */
.add-trigger {
  position: absolute;
  bottom: 10px;
  right: 10px;
  background: #ff4d6d;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  transition: 0.2s;
}

.add-trigger:hover {
  transform: scale(1.1);
  background: #ff6b86;
}

/* FOOTER */
footer {
  position: sticky;
  bottom: 0;
  background: #15151d;
  padding: 15px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* CART */
.cart-summary {
  font-size: 14px;
}

/* BUTTON */
.checkout-btn {
  background: #ff4d6d;
  border: none;
  padding: 10px 16px;
  border-radius: 10px;
  color: white;
  cursor: pointer;
  transition: 0.2s;
  font-size: 16px;
}

.checkout-btn:hover {
  background: #ff6b86;
}

.categories::-webkit-scrollbar {
  display: none;
}

.categories {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
</style>