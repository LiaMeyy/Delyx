<template>
  <header>
    <h1>Delyx</h1>
    <input type="text" v-model="searchTerm" class="search-bar" placeholder="🔍 Buscar plato o bebida..." />
    <button type="button" class="add-product-btn" @click="abrirModalNuevoProducto">+ Agregar producto</button>
  </header>
  <main>
    <div class="categories">
      <div class="cat-chip" :class="{ active: categoriaActiva === 'todo' }" @click="categoriaActiva = 'todo'">Todo</div>
      <div class="cat-chip" :class="{ active: categoriaActiva === 'entradas' }" @click="categoriaActiva = 'entradas'">
        Entradas</div>
      <div class="cat-chip" :class="{ active: categoriaActiva === 'fuertes' }" @click="categoriaActiva = 'fuertes'">
        Fuertes</div>
      <div class="cat-chip" :class="{ active: categoriaActiva === 'cocteles' }" @click="categoriaActiva = 'cocteles'">
        Cocteles</div>
      <div class="cat-chip" :class="{ active: categoriaActiva === 'vinos' }" @click="categoriaActiva = 'vinos'">Vinos
      </div>
    </div>
    <div class="content">
      <div class="items-grid">
        <div class="item-card" v-for="item in itemsFiltrados()" :key="item.name" @click="abrirModalProducto(item)">
          <img :src="item.image" :alt="item.name" class="item-thumb" />
          <div class="card-info">
            <span class="item-name">{{ item.name }}</span>
            <span class="item-desc">{{ item.descripcion }}</span>
            <div style="display: flex; justify-content: space-between; align-items: center; margin-top: 4px;">
             <span class="item-price">${{ item.price.toLocaleString() }}</span>
              <span class="item-stock">Stock: {{ item.stock }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- BOTÓN FLOTANTE STICKY DEL CARRITO -->
    <button class="cart-button-sticky" @click="abrirCarrito">
      🛒 {{ carrito.length }}
    </button>

    <!-- MODAL PARA AGREGAR PRODUCTO -->
    <div class="modal-overlay" v-if="modalProductoAbierto" @click="cerrarModalProducto">
      <div class="modal-agregar-producto" @click.stop>
        <div class="modal-header">
          <h1>{{ productoSeleccionado?.name }}</h1>
          <button class="btn-cerrar" @click="cerrarModalProducto">✕</button>
        </div>

        <!-- IMAGEN Y DESCRIPCIÓN -->
        <div class="producto-detalle">
          <img :src="productoSeleccionado?.image" :alt="productoSeleccionado?.name" class="modal-imagen" />
          <p class="modal-descripcion">{{ productoSeleccionado?.descripcion }}</p>
          <p class="modal-precio">Precio: <span style="font-size: 20px; font-weight: bold; color: #ff4d6d;">${{
            productoSeleccionado?.price.toLocaleString() }}</span></p>
          <p class="modal-stock" v-if="productoSeleccionado?.stock > 0">Stock disponible: {{ productoSeleccionado?.stock
          }}</p>
          <p class="modal-sin-stock" v-else>Sin stock</p>
        </div>

        <div class="grey-bar"></div>

        <!-- CANTIDAD -->
        <div class="seccion-cantidad">
          <h3>Cantidad</h3>
          <div class="cantidad-selector">
            <button class="btn-cantidad" @click="restarCantidadModal" :disabled="cantidadModal <= 1">-</button>
            <input type="number" v-model.number="cantidadModal" class="input-cantidad" min="1" />
            <button class="btn-cantidad" @click="sumarCantidadModal"
              :disabled="cantidadModal >= productoSeleccionado?.stock">+</button>
          </div>
        </div>

        <!-- OBSERVACIONES -->
        <div class="seccion-observaciones">
          <h3>Observaciones del producto (opcional)</h3>
          <textarea v-model="observacionesModal" class="textarea-observaciones"
            placeholder="Ej: Sin picante, extra queso, sin cebolla..."></textarea>
        </div>

        <div class="grey-bar"></div>

        <!-- BOTÓN AGREGAR -->
        <button class="btn-agregar-carrito" @click="agregarAlCarritoConDetalles"
          :disabled="cantidadModal <= 0 || !productoSeleccionado">
          Agregar al carrito
        </button>
      </div>
    </div>

    <!-- MODAL PARA AGREGAR PRODUCTO NUEVO -->
    <div class="modal-overlay" v-if="modalNuevoProductoAbierto" @click="cerrarModalNuevoProducto">
      <div class="modal-agregar-producto" @click.stop>
        <div class="modal-header">
          <h1>Agregar Producto</h1>
          <button class="btn-cerrar" @click="cerrarModalNuevoProducto">✕</button>
        </div>
        <div class="alert" v-if="alertNuevoProducto">{{ alertNuevoProducto }}</div>
        <div class="producto-detalle">
          <div class="form-field">
            <label class="form-label">Nombre</label>
            <input v-model="nuevoProducto.name" class="form-input" type="text" placeholder="Nombre del producto" />
          </div>
          <div class="form-field">
            <label class="form-label">Precio</label>
            <input v-model.number="nuevoProducto.price" class="form-input" type="number" min="0"
              placeholder="Precio en pesos" />
          </div>
          <div class="form-field">
            <label class="form-label">Categoría</label>
            <select v-model="nuevoProducto.category" class="form-select">
              <option value="entradas">Entradas</option>
              <option value="fuertes">Fuertes</option>
              <option value="cocteles">Cocteles</option>
              <option value="vinos">Vinos</option>
            </select>
          </div>
          <div class="form-field">
            <label class="form-label">Descripción</label>
            <textarea v-model="nuevoProducto.descripcion" class="form-textarea"
              placeholder="Descripción del producto"></textarea>
          </div>
          <div class="form-field">
            <label class="form-label">Stock</label>
            <input v-model.number="nuevoProducto.stock" class="form-input" type="number" min="0"
              placeholder="Cantidad disponible" />
          </div>
          <div class="form-field">
            <label class="form-label">Link de imagen</label>
            <input v-model="nuevoProducto.image" class="form-input" type="text"
              placeholder="URL de la imagen (opcional)" />
          </div>

          <button type="button" class="btn-agregar-carrito" @click="agregarProductoNuevo">Guardar producto</button>
        </div>
      </div>
    </div>

    <!-- MODAL DEL CARRITO -->
    <div class="modal-overlay" v-if="modalAbierto" @click="cerrarCarrito">
      <div class="modal-carrito" @click.stop>
        <div v-if="cargando" class="spinner-overlay">
          <div class="spinner"></div>
          <p>Procesando pedido...</p>
        </div>
        <div class="modal-header">
          <h1>Mi Cuenta</h1>
          <button class="btn-cerrar" @click="cerrarCarrito">✕</button>
        </div>

        <div class="titulos_P_C">
          <h2>Producto</h2>
          <h2>Cantidad</h2>
        </div>
        <div class="grey-bar"></div>

        <!-- PRODUCTOS EN EL CARRITO -->
        <div class="items-carrito">
          <div v-if="carrito.length === 0" class="carrito-vacio">
            El carrito está vacío 🍽️
          </div>
          <div v-for="(itemCarrito, index) in carrito" :key="index" class="Producto_Cantidad">
            <div class="producto">
              <div class="nombre_producto">{{ itemCarrito.name }}</div>
              <div v-if="itemCarrito.observaciones" class="observaciones-carrito">
                📝 {{ itemCarrito.observaciones }}
              </div>
            </div>
            <div class="Cantidad_Precio">
              <div class="cantidad">
                <button class="boton_cantidad" @click="restarCantidad(index)">-</button>
                <span class="cuadro_Cantidad">{{ itemCarrito.cantidad }}</span>
                <button class="boton_cantidad" @click="sumarCantidad(index)">+</button>
              </div>
              <div class="precio">
                x $ {{ itemCarrito.price.toLocaleString() }} = <span style="font-weight: bold;">$ {{ (itemCarrito.price
                  * itemCarrito.cantidad).toLocaleString() }}</span>
              </div>
              <button class="btn-eliminar" @click="eliminarDelCarrito(index)">🗑️</button>
            </div>
          </div>
        </div>

        <div class="grey-bar"></div>

        <!-- TOTAL -->
        <div class="total-carrito">
          <h2>Total: $ {{ totalCarrito.toLocaleString() }}</h2>
        </div>

        <div class="campo-mesa">
          <label>Número de mesa</label>
          <input type="number" v-model.number="mesaActual" placeholder="Ej: 5" min="1" class="form-input" />
        </div>
        <button class="checkout-btn" @click="finalizarPedido">Finalizar Pedido</button>
      </div>

    </div>
    <!-- MODAL FACTURA -->
    <div class="modal-overlay" v-if="modalFacturaAbierto" @click="cerrarFactura">
      <div class="modal-factura" @click.stop>

        <!-- ENCABEZADO -->
        <div class="factura-header">
          <div class="factura-logo">
            <strong>DELYX</strong><br />
            <span>Restaurante & Bar</span>
          </div>
          <div class="factura-no">NO. {{ numeroFactura }}</div>
        </div>

        <h1 class="factura-titulo">FACTURA</h1>

        <p class="factura-fecha"><strong>Fecha:</strong> {{ fechaFactura }}</p>

        <!-- BILLED TO / FROM -->
        <div class="factura-partes">
          <div>
            <p><strong>Facturado a:</strong></p>
            <p>Mesa {{ mesaFactura }}</p>
            <p>Delyx Restaurant</p>
            <p>Cra. 7 #62-35, Bogotá</p>
            <p>delyx@restaurante.com</p>
          </div>
          <div>
            <p><strong>De:</strong></p>
            <p>Delyx S.A.S</p>
            <p>Cra. 7 #62-35, Bogotá</p>
            <p>NIT: 901.234.567-8</p>
            <p>delyx@restaurante.com</p>
          </div>
        </div>

        <!-- TABLA DE ITEMS -->
        <table class="factura-tabla">
          <thead>
            <tr>
              <th>Producto</th>
              <th>Cantidad</th>
              <th>Precio</th>
              <th>Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in carrito" :key="item.name">
              <td>{{ item.name }}</td>
              <td style="text-align: center;">{{ item.cantidad }}</td>
              <td style="text-align: right;">${{ item.price.toLocaleString() }}</td>
              <td style="text-align: right;">${{ (item.price * item.cantidad).toLocaleString() }}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="3" style="text-align: right;"><strong>Total</strong></td>
              <td style="text-align: right;"><strong>${{ totalCarrito.toLocaleString() }}</strong></td>
            </tr>
          </tfoot>
        </table>

        <!-- PIE -->
        <div class="factura-pie">
          <p><strong>Método de pago:</strong> Efectivo</p>
          <p><strong>Nota:</strong> ¡Gracias por elegir Delyx! Vuelve pronto.</p>
        </div>

        <!-- BOTONES -->
        <div class="factura-acciones">
          <button class="btn-cerrar-factura" @click="cerrarFactura">Cerrar</button>
          <button class="btn-imprimir" @click="imprimirFactura">Imprimir / Guardar PDF</button>
        </div>

      </div>
    </div>

    <!-- NOTIFICACIONES -->
    <div v-if="mostrarNotif" :class="['notificacion', tipoNotificacion]">
      {{ notificacion }}
    </div>
  </main>


</template>

<script setup>
import { ref, reactive } from 'vue'

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

// VARIABLES PARA FACTURA 
const modalFacturaAbierto = ref(false)
const numeroFactura = ref('')
const fechaFactura = ref('')
const mesaFactura = ref('')
const mesaActual = ref('')
// CARRITO
const carrito = ref([])
const modalAbierto = ref(false)

// MODAL PARA AGREGAR PRODUCTO
const cargando = ref(false)
const categoriaActiva = ref('todo')
const modalProductoAbierto = ref(false)
const modalNuevoProductoAbierto = ref(false)
const productoSeleccionado = ref(null)
const cantidadModal = ref(1)
const observacionesModal = ref('')
const nuevoProducto = reactive({
  name: '',
  price: 0,
  category: 'entradas',
  descripcion: '',
  stock: 0,
  image: ''
})
const alertNuevoProducto = ref('')

// BÚSQUEDA
const searchTerm = ref('')

// NOTIFICACIONES
const notificacion = ref('')
const tipoNotificacion = ref('success')
const mostrarNotif = ref(false)

// FILTRAR ITEMS POR BÚSQUEDA
function itemsFiltrados() {
  let resultado = items.value

  if (categoriaActiva.value !== 'todo') {
    resultado = resultado.filter(item => item.category === categoriaActiva.value)
  }

  if (searchTerm.value) {
    const term = searchTerm.value.toLowerCase()
    resultado = resultado.filter(item =>
      item.name.toLowerCase().includes(term) ||
      item.descripcion.toLowerCase().includes(term)
    )
  }

  return resultado
}

// ABRIR Y CERRAR MODAL DEL CARRITO
function abrirCarrito() {
  modalAbierto.value = true
}

function cerrarCarrito() {
  modalAbierto.value = false
}

// ABRIR Y CERRAR MODAL DEL PRODUCTO
function abrirModalProducto(item) {
  productoSeleccionado.value = item
  cantidadModal.value = 1
  observacionesModal.value = ''
  modalProductoAbierto.value = true
}

function cerrarModalProducto() {
  modalProductoAbierto.value = false
  productoSeleccionado.value = null
  cantidadModal.value = 1
  observacionesModal.value = ''
}

function abrirModalNuevoProducto() {
  resetNuevoProducto()
  modalNuevoProductoAbierto.value = true
}

function cerrarModalNuevoProducto() {
  modalNuevoProductoAbierto.value = false
  resetNuevoProducto()
}

function resetNuevoProducto() {
  nuevoProducto.name = ''
  nuevoProducto.price = 0
  nuevoProducto.category = 'entradas'
  nuevoProducto.descripcion = ''
  nuevoProducto.stock = 0
  nuevoProducto.image = ''
  alertNuevoProducto.value = ''
}

function validarProductoNuevo() {
  if (!nuevoProducto.name.trim()) {
    return 'El nombre es obligatorio.'
  }
  if (!nuevoProducto.descripcion.trim()) {
    return 'La descripción es obligatoria.'
  }
  if (nuevoProducto.price <= 0) {
    return 'El precio debe ser mayor a 0.'
  }
  if (!nuevoProducto.category) {
    return 'Selecciona una categoría.'
  }
  if (nuevoProducto.stock <= 0) {
    return 'El stock debe ser mayor a 0.'
  }
  return ''
}

// CONTROLAR CANTIDAD EN EL MODAL
function sumarCantidadModal() {
  if (cantidadModal.value < productoSeleccionado.value.stock) {
    cantidadModal.value++
  }
}

function agregarProductoNuevo() {
  const error = validarProductoNuevo()
  if (error) {
    alertNuevoProducto.value = error
    return
  }

  const producto = nuevoProducto

  const itemParaAgregar = {
    name: producto.name,
    price: Number(producto.price),
    image: producto.image || 'https://images.unsplash.com/photo-1504674900247-0877df9cc836?w=400&q=80',
    category: producto.category,
    descripcion: producto.descripcion,
    stock: Number(producto.stock)
  }

  items.value.push(itemParaAgregar)
  categoriaActiva.value = 'todo'
  mostrarNotificacion(`✅ Producto ${producto.name} agregado correctamente`)
  cerrarModalNuevoProducto()
}

function restarCantidadModal() {
  if (cantidadModal.value > 1) {
    cantidadModal.value--
  }
}

// AGREGAR AL CARRITO CON OBSERVACIONES
function agregarAlCarritoConDetalles() {
  const item = productoSeleccionado.value

  if (!item || cantidadModal.value <= 0) {
    mostrarNotificacion('❌ Por favor selecciona una cantidad válida', 'error')
    return
  }

  if (cantidadModal.value > item.stock) {
    mostrarNotificacion('❌ No hay suficiente stock disponible', 'error')
    return
  }

  // Buscar si el producto ya existe en el carrito (con las mismas observaciones)
  const existe = carrito.value.find(p => p.name === item.name && p.observaciones === observacionesModal.value)

  if (existe) {
    // Si ya existe con las mismas observaciones, sumar cantidad
    existe.cantidad += cantidadModal.value
  } else {
    // Si no existe, agregarlo con observaciones
    carrito.value.push({
      ...item,
      cantidad: cantidadModal.value,
      observaciones: observacionesModal.value
    })
  }

  // Actualizar total
  actualizarTotal()

  // Mostrar notificación
  mostrarNotificacion(`✅ ${item.name} (x${cantidadModal.value}) agregado al carrito`)

  // Cerrar modal
  cerrarModalProducto()
}

// SUMAR CANTIDAD
function sumarCantidad(index) {
  carrito.value[index].cantidad++
  actualizarTotal()
}

// RESTAR CANTIDAD
function restarCantidad(index) {
  if (carrito.value[index].cantidad > 1) {
    carrito.value[index].cantidad--
    actualizarTotal()
  }
}

// ELIMINAR DEL CARRITO
function eliminarDelCarrito(index) {
  carrito.value.splice(index, 1)
  actualizarTotal()
}

// CALCULAR TOTAL (sin computed, solo una función)
function calcularTotal() {
  let total = 0
  carrito.value.forEach(item => {
    total += item.price * item.cantidad
  })
  return total
}

// VARIABLE PARA TOTAL (se actualiza cada vez que cambias el carrito)
const totalCarrito = ref(0)

// FUNCIONAR QUE ACTUALIZA EL TOTAL CUANDO CAMBIAS EL CARRITO
function actualizarTotal() {
  totalCarrito.value = calcularTotal()
}

// MOSTRAR NOTIFICACIÓN
function mostrarNotificacion(mensaje, tipo = 'success') {
  notificacion.value = mensaje
  tipoNotificacion.value = tipo
  mostrarNotif.value = true
  setTimeout(() => {
    mostrarNotif.value = false
  }, 3000)
}

// FINALIZAR PEDIDO
function finalizarPedido() {
  if (carrito.value.length === 0) {
    mostrarNotificacion('❌ El carrito está vacío', 'error')
    return
  }
  if (!mesaActual.value || mesaActual.value <= 0) {
    mostrarNotificacion('❌ Ingresa un número de mesa válido', 'error')
    return
  }

  cargando.value = true

  setTimeout(() => {
    carrito.value.forEach(itemCarrito => {
      const item = items.value.find(i => i.name === itemCarrito.name)
      if (item) item.stock -= itemCarrito.cantidad
    })

    // Datos para la factura
    const ahora = new Date()
    fechaFactura.value = ahora.toLocaleDateString('es-CO', { day: '2-digit', month: 'long', year: 'numeric' })
    numeroFactura.value = String(Math.floor(Math.random() * 900000) + 100000)
    mesaFactura.value = mesaActual.value

    actualizarTotal()
    cargando.value = false
    modalAbierto.value = false
    modalFacturaAbierto.value = true

  }, 1500)
}

function cerrarFactura() {
  modalFacturaAbierto.value = false
  carrito.value = []
  mesaActual.value = ''
  actualizarTotal()
}

function imprimirFactura() {
  window.print()
}

// Actualizar total cada que haya cambios
actualizarTotal()
</script>