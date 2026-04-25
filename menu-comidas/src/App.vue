<template>
  <div class="contenedor-principal">
    <header class="barra-superior">
      <div class="ajuste-ancho">
        <div class="marca-tienda">
          <div class="caja-logo">
            <img src="https://cdn-icons-png.flaticon.com/512/737/737967.png" alt="Logo" class="icono-logo-img">
          </div>
          <div class="nombres">
            <h1 class="titulo-negocio">BURG & <span class="naranja">BITE</span></h1>
            <p class="ciudad">San Gil, Santander</p>
          </div>
        </div>

        <div class="boton-carrito" @click="toggleCarrito" style="cursor: pointer;">
          <div class="circulo-icono">
            <svg xmlns="http://www.w3.org/2000/svg" class="svg-carrito" fill="none" viewBox="0 0 24 24"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" />
            </svg>
            <span v-if="cantidadCarrito > 0" class="bolita-roja">
              {{ cantidadCarrito }}
            </span>
          </div>
        </div>
      </div>
    </header>

    <nav class="selector-categorias">
      <div class="flex-categorias">
        <button v-for="cat in listaCategorias" :key="cat.id" class="pestaña"
          :class="{ 'pestaña-activa': cat.clave === categoriaActiva }" @click="cambiarSeccion(cat.clave)">
          {{ cat.nombre }}
        </button>
      </div>
    </nav>

    <main class="seccion-comida">
      <div class="rejilla-productos">

        <div v-for="comida in productos" :key="comida.id" class="tarjeta-contenedor">
          <div class="tarjeta-inner" :class="{ 'es-volteada': comida.volteada }">

            <div class="tarjeta-frente">
              <div class="contenedor-foto">
                <img :src="comida.imagen" :alt="comida.nombre" class="foto-producto-web">
              </div>
              <div class="detalles">
                <h3 class="nombre-comida">{{ comida.nombre }}</h3>
                <p class="precio-comida">${{ comida.precio }}</p>
                <p class="stock" :class="{ 'alerta-stock': comida.disponibilidad < 5 }">
                  {{ comida.disponibilidad > 0 ? 'Disponibles: ' + comida.disponibilidad : 'Agotado' }}
                </p>
                <button v-if="comida.ingredientes" class="boton-info" @click="voltear(comida)">
                  🔍 Ingredientes
                </button>
                <button class="boton-comprar" :class="{ 'boton-agotado': comida.disponibilidad === 0 }"
                  @click="agregarAlcarrito(comida)" :disabled="comida.disponibilidad === 0">
                  {{ comida.disponibilidad > 0 ? 'Agregar al carrito' : 'Agotado' }}
                </button>
              </div>
            </div>
            <div class="tarjeta-atras">
              <div class="contenido-atras">
                <h3>Ingredientes</h3>
                <p>{{ comida.ingredientes }}</p>
                <span class="tocar-para-volver">Click para volver</span>
                <button class="boton-volver" @click.stop="voltear(comida)">Volver</button>
              </div>
            </div>

          </div>
        </div>

      </div>
    </main>

    <footer class="pie-final">
      <p>Desarrollado para Burg & Bite - 2026</p>
    </footer>
    <div v-if="mostrarCarrito" class="overlay" @click="toggleCarrito"></div>
    <aside v-if="mostrarCarrito" class="panel-carrito">
      <div class="encabezado-carrito">
        <div class="logo-carrito">
          <img src="https://cdn-icons-png.flaticon.com/512/737/737967.png" alt="Logo">
          <div>
            <h2 class="titulo-negocio">BURG & <span class="naranja">BITE</span></h2>
            <p class="lema">¡Sabor en cada mordisco!</p>
          </div>
        </div>
        <button class="boton-cerrar" @click="toggleCarrito">X</button>
      </div>
      <div class="lista-items">
        <p v-if="carrito.length === 0" class="carrito-vacio">Tu carrito está vacío</p>
        <div v-for="(item, index) in carrito" :key="index" class="item-carrito">
          <img :src="item.imagen" alt="foto">
          <div class="item-info">
            <p class="item-nombre">{{ item.nombre }}</p>
            <p class="item-precio">{{ item.precio }}</p>
            <button class="boton-eliminar" @click="eliminarDelCarrito(index)">
              🗑️
            </button>
          </div>
        </div>
      </div>
      <div class="pie-carrito">
        <div class="total-seccion">
          <span>Total a pagar:</span>
          <span class="monto-total">${{ totalDinero.toLocaleString('es-CO') }}</span>
        </div>
        <button class="boton-pedir" @click="confirmarPedido">Confirmar Pedido</button>
      </div>
    </aside>

    <div v-if="alertaVisible" class="toast-personalizado" :class="alertaTipo">
      <div class="icono-alerta">
        <span v-if="alertaTipo === 'exito'">✅</span>
        <span v-if="alertaTipo === 'error'">❌</span>
        <span v-if="alertaTipo === 'info'">ℹ️</span>
      </div>
      <p>{{ alertaMensaje }}</p>
    </div>

    <div v-if="cargando" class="pantalla-carga">
      <div class="spinner"></div>
      <p>Enviando tu pedido a la cocina...</p>
    </div>
    <div v-if="mostrarFactura" class="overlay-factura">
      <div class="card-factura">
        <button class="cerrar-factura" @click="mostrarFactura = false">X</button>
        <div id="seccion-factura" class="contenido-factura">
          <div class="header-factura">
            <img src="https://cdn-icons-png.flaticon.com/512/737/737967.png" alt="Logo">
            <h1>BURG & <span class="naranja">BITE</span></h1>
            <p>¡Sabor en cada mordisco!</p>
            <hr>
          </div>
          <div class="info-pedido">
            <p><strong>Nro. Pedido:</strong>#{{ datosFactura.id }}</p>
            <p><strong>Fecha:</strong>{{ datosFactura.fecha }}</p>
            <p><strong>Ubicación:</strong>San Gil, Santander</p>
          </div>
          <table class="tabla-factura">
            <thead>
              <tr>
                <th>Producto</th>
                <th>Precio</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in datosFactura.items" :key="index">
                <td>{{ item.nombre }}</td>
                <td>${{ item.precio }}</td>
              </tr>
            </tbody>
          </table>
          <div class="total-factura">
            <h3>Total Pagado: ${{ datosFactura.total.toLocaleString('es-CO') }}</h3>
          </div>
          <p class="gracias">¡Gracias por tu compra! Te esperamos pronto.</p>
        </div>
        <button class="boton-descargar" @click="descargarPDF">
          Descargar Factura (PDF) 📄
        </button>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue';

const listaCategorias = ref([
  { id: 1, nombre: 'Hamburguesas', clave: 'hamburguesas' },
  { id: 2, nombre: 'Hotdogs', clave: 'hotdogs' },
  { id: 3, nombre: 'Pizzas', clave: 'pizzas' },
  { id: 4, nombre: 'Salchipapas', clave: 'salchipapas' },
  { id: 5, nombre: 'Postres', clave: 'postres' },
  { id: 6, nombre: 'Bebidas', clave: 'bebidas' },
  { id: 7, nombre: 'Adicionales', clave: 'adicionales' }
]);

// 1. AQUÍ ESTÁ LA NUEVA BASE DE DATOS UNIFICADA
const todosLosProductos = [
  // Hamburguesas
  { id: 101, categoria: 'hamburguesas', nombre: 'Doble Queso XL', precio: '25.900', imagen: 'https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=500', disponibilidad: 10, ingredientes: ['Pan artesanal', 'Doble carne de res', 'Doble queso cheddar', 'Lechuga', 'Tomate', 'Salsa de la casa'] },
  { id: 102, categoria: 'hamburguesas', nombre: 'Clásica Burg', precio: '18.500', imagen: 'https://images.unsplash.com/photo-1594212699903-ec8a3eca50f5?w=500', disponibilidad: 12, ingredientes: ['Pan ajonjolí', 'Carne de res', 'Queso mozzarella', 'Lechuga', 'Tomate', 'Cebolla', 'Mayonesa', 'Salsa de tomate'] },
  { id: 103, categoria: 'hamburguesas', nombre: 'Mexicana Spicy', precio: '24.900', imagen: 'https://images.unsplash.com/photo-1586190848861-99aa4a171e90?w=500', disponibilidad: 12, ingredientes: ['Pan artesanal', 'Carne de res', 'Queso pepper jack', 'Guacamole', 'Jalapeños', 'Pico de gallo', 'Nachos triturados'] },
  { id: 104, categoria: 'hamburguesas', nombre: 'Pollo Crispy', precio: '21.000', imagen: 'https://static.wixstatic.com/media/29cc8e_d8a25a27f2f640558f6723144341d662~mv2.jpg/v1/fill/w_740,h_493,al_c,q_85,usm_0.66_1.00_0.01,enc_avif,quality_auto/29cc8e_d8a25a27f2f640558f6723144341d662~mv2.jpg', disponibilidad: 12, ingredientes: ['Pan brioche', 'Pechuga de pollo apanada', 'Queso sabana', 'Lechuga', 'Tomate', 'Salsa tártara'] },
  { id: 105, categoria: 'hamburguesas', nombre: 'Triple Carne', precio: '32.000', imagen: 'https://images.unsplash.com/photo-1553979459-d2229ba7433b?w=500', disponibilidad: 12, ingredientes: ['Pan artesanal', 'Triple carne de res', 'Triple queso cheddar', 'Tocineta', 'Cebolla caramelizada', 'Salsa BBQ'] },
  { id: 106, categoria: 'hamburguesas', nombre: 'Veggie Delicia', precio: '22.500', imagen: 'https://images.unsplash.com/photo-1550547660-d9450f859349?w=500', disponibilidad: 12, ingredientes: ['Pan integral', 'Croqueta de lentejas y garbanzos', 'Queso asado', 'Lechuga', 'Tomate', 'Cebolla', 'Salsa de ajo'] },
  // Hotdogs
  { id: 201, categoria: 'hotdogs', nombre: 'Perro Sencillo', precio: '12.000', imagen: 'https://images.unsplash.com/photo-1541214113241-21578d2d9b62?w=500', disponibilidad: 12, ingredientes: ['Pan de perro', 'Salchicha tradicional', 'Cebolla picada', 'Papas ripio', 'Salsa de tomate', 'Mayonesa'] },
  { id: 202, categoria: 'hotdogs', nombre: 'Perro con Todo', precio: '15.500', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRE_PEx-YRJA0XpZ9pF47f7NqroIueFnKNt9Q&s', disponibilidad: 12, ingredientes: ['Pan de perro', 'Salchicha americana', 'Queso fundido', 'Tocineta', 'Huevo de codorniz', 'Cebolla', 'Papas ripio', 'Salsas'] },
  { id: 203, categoria: 'hotdogs', nombre: 'Perro Suizo', precio: '18.900', imagen: 'https://tofuu.getjusto.com/orioneat-local/resized2/D65379QMuyRwuZLyt-x-800.webp', disponibilidad: 12, ingredientes: ['Pan de perro', 'Salchicha suiza', 'Queso mozzarella gratinado', 'Crema de leche', 'Papas ripio'] },
  { id: 204, categoria: 'hotdogs', nombre: 'Perro Ranchero', precio: '16.000', imagen: 'https://images.rappi.com/restaurants_background/colosperrosdelmono-1675777013143.jpg', disponibilidad: 12, ingredientes: ['Pan de perro', 'Salchicha', 'Chorizo picado', 'Maíz tierno', 'Queso fundido', 'Papas ripio', 'Salsa BBQ'] },
  { id: 205, categoria: 'hotdogs', nombre: 'Mega Perro Bite', precio: '22.000', imagen: 'https://images.rappi.com/restaurants_background/portada-1683125522549.jpg', disponibilidad: 12, ingredientes: ['Pan grande', 'Doble salchicha', 'Carne desmechada', 'Pollo desmechado', 'Queso doble crema', 'Tocineta', 'Papas ripio'] },
  { id: 206, categoria: 'hotdogs', nombre: 'Perro Kids', precio: '10.500', imagen: 'https://images.unsplash.com/photo-1599599810694-b5b37304c041?w=500', disponibilidad: 12, ingredientes: ['Pan suave', 'Salchicha pequeña', 'Queso rallado', 'Salsa de tomate suave'] },
  // Pizzas
  { id: 301, categoria: 'pizzas', nombre: 'Pepperoni Clásica', precio: '35.000', imagen: 'https://images.unsplash.com/photo-1628840042765-356cda07504e?w=500', disponibilidad: 12, ingredientes: ['Masa artesanal', 'Salsa napolitana', 'Queso mozzarella', 'Doble pepperoni', 'Orégano'] },
  { id: 302, categoria: 'pizzas', nombre: 'Hawaiana Especial', precio: '32.500', imagen: 'https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?w=500', disponibilidad: 12, ingredientes: ['Masa artesanal', 'Salsa napolitana', 'Queso mozzarella', 'Jamón ahumado', 'Trozos de piña melada', 'Cerezas'] },
  { id: 303, categoria: 'pizzas', nombre: 'Pollo y Champiñones', precio: '34.900', imagen: 'https://images.unsplash.com/photo-1513104890138-7c749659a591?w=500', disponibilidad: 12, ingredientes: ['Masa artesanal', 'Salsa blanca', 'Queso mozzarella', 'Pollo desmechado', 'Champiñones frescos', 'Orégano'] },
  { id: 304, categoria: 'pizzas', nombre: 'Carnívora Premium', precio: '42.000', imagen: 'https://tofuu.getjusto.com/orioneat-local/resized2/7Lma3qmnxQnvcyovr-2400-x.webp', disponibilidad: 12, ingredientes: ['Masa artesanal', 'Salsa napolitana', 'Queso mozzarella', 'Pepperoni', 'Cábano', 'Jamón', 'Tocineta', 'Salami'] },
  { id: 305, categoria: 'pizzas', nombre: 'Vegetariana Huerto', precio: '29.900', imagen: 'https://images.unsplash.com/photo-1571407970349-bc81e7e96d47?w=500', disponibilidad: 12, ingredientes: ['Masa artesanal', 'Salsa napolitana', 'Queso mozzarella', 'Pimentón', 'Cebolla', 'Champiñones', 'Aceitunas negras', 'Maíz'] },
  { id: 306, categoria: 'pizzas', nombre: 'Mexicana con Nachos', precio: '38.500', imagen: 'https://img.freepik.com/fotos-premium/pizza-mexicana-mozzarella-cebolla-pepperoni-aceituna-negra-pimientos-verdes-nachos-oregano-pizza-mexicana-vista-superior_311876-471.jpg', disponibilidad: 12, ingredientes: ['Masa artesanal', 'Salsa napolitana', 'Queso mozzarella', 'Carne molida', 'Jalapeños', 'Cebolla', 'Nachos', 'Guacamole'] },
  // Salchipapas
  { id: 401, categoria: 'salchipapas', nombre: 'Senci-Papa', precio: '14.000', imagen: 'https://imag.bonviveur.com/emplatado-final-de-las-salchipapas.jpg', disponibilidad: 12, ingredientes: ['Papas a la francesa', 'Salchicha tradicional picada', 'Salsa de tomate', 'Mayonesa', 'Salsa rosada'] },
  { id: 402, categoria: 'salchipapas', nombre: 'Salchi-Especial', precio: '19.500', imagen: 'https://peru.info/cms/files/publicacion/publicacion_20241115_123829.jpg', disponibilidad: 12, ingredientes: ['Papas a la francesa', 'Salchicha americana', 'Queso fundido', 'Tocineta crujiente', 'Huevo de codorniz', 'Salsas de la casa'] },
  { id: 403, categoria: 'salchipapas', nombre: 'Costeña Power', precio: '24.000', imagen: 'https://tb-static.uber.com/prod/image-proc/processed_images/fdeac4399e5a9b47d190a99a672edeb3/bc9c318a9c96996e2d990faf2b0c65f6.jpeg', disponibilidad: 12, ingredientes: ['Papas a la francesa', 'Salchicha manguera', 'Butifarra', 'Chorizo', 'Bollo limpio', 'Queso costeño rallado', 'Suero costeño'] },
  { id: 404, categoria: 'salchipapas', nombre: 'Criolla Tradición', precio: '18.000', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTaF0dMQK1sgfC_22Bxr9YyrFHUhaQoT_CXrQ&s', disponibilidad: 12, ingredientes: ['Papas criollas fritas', 'Salchicha ranchera', 'Hogao casero', 'Queso rallado', 'Cilantro fresco'] },
  { id: 405, categoria: 'salchipapas', nombre: 'Burg & Bite Mix', precio: '28.500', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSzQ_byQy1rF3XU_nNNGRkSKXruaqQ8vIyvNQ&s', disponibilidad: 12, ingredientes: ['Papas a la francesa', 'Salchicha picada', 'Carne de hamburguesa', 'Pollo desmechado', 'Queso doble crema', 'Maíz', 'Tocineta'] },
  { id: 406, categoria: 'salchipapas', nombre: 'Salchi-Infantil', precio: '12.000', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS6aPXulQpr9vfvC6v97Ec8HSlNSHfsWMlGFA&s', disponibilidad: 12, ingredientes: ['Papas a la francesa', 'Salchicha suave', 'Salsa de tomate'] },
  // Postres
  { id: 501, categoria: 'postres', nombre: 'Brownie con Helado', precio: '12.500', imagen: 'https://images.unsplash.com/photo-1606313564200-e75d5e30476c?w=500', disponibilidad: 12, ingredientes: ['Brownie caliente', 'Helado de vainilla', 'Salsa de chocolate', 'Nuez'] },
  { id: 502, categoria: 'postres', nombre: 'Cheesecake Frutos Rojos', precio: '14.000', imagen: 'https://images.unsplash.com/photo-1533134242443-d4fd215305ad?w=500', disponibilidad: 12, ingredientes: ['Base de galleta', 'Crema de queso', 'Mermelada frutos rojos', 'Fresas'] },
  { id: 503, categoria: 'postres', nombre: 'Waffle Nutella', precio: '15.900', imagen: 'https://images.unsplash.com/photo-1519915028121-7d3463d20b13?w=500', disponibilidad: 12, ingredientes: ['Waffle', 'Nutella', 'Banano', 'Fresas', 'Azúcar pulverizada'] },
  { id: 504, categoria: 'postres', nombre: 'Malteada de Oreo', precio: '13.500', imagen: 'https://images.unsplash.com/photo-1572490122747-3968b75cc699?w=500', disponibilidad: 12, ingredientes: ['Helado de vainilla', 'Leche', 'Galletas Oreo', 'Chantilly', 'Chocolate'] },
  { id: 505, categoria: 'postres', nombre: 'Pie de Limón', precio: '10.000', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQEOadD1Mhm6DZkxmy9AYdCIQ6XrFSoyUryWw&s', disponibilidad: 12, ingredientes: ['Base de galleta', 'Crema de limón', 'Merengue', 'Ralladura'] },
  { id: 506, categoria: 'postres', nombre: 'Copa de Helado Mix', precio: '9.500', imagen: 'https://images.unsplash.com/photo-1563805042-7684c019e1cb?w=500', disponibilidad: 12, ingredientes: ['Helado variado', 'Barquillo', 'Caramelo', 'Lluvia de colores'] },
  // Bebidas
  { id: 601, categoria: 'bebidas', nombre: 'Coca-Cola 1.5L', precio: '8.500', imagen: 'https://licoresjunior.com/wp-content/uploads/2023/12/1.5-LITROS.jpg', disponibilidad: 12, ingredientes: ['Agua carbonatada', 'Azúcar', 'Colorante', 'Cafeína'] },
  { id: 602, categoria: 'bebidas', nombre: 'Limonada Natural', precio: '7.000', imagen: 'https://images.unsplash.com/photo-1513558161293-cdaf765ed2fd?w=500', disponibilidad: 12, ingredientes: ['Agua', 'Zumo de limón', 'Azúcar', 'Hielo'] },
  { id: 603, categoria: 'bebidas', nombre: 'Jugo de Mora', precio: '7.500', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTKiKz8HjM460TIEPFJpkRvRu1ydydQf0kSsg&s', disponibilidad: 12, ingredientes: ['Agua o Leche', 'Mora natural', 'Azúcar'] },
  { id: 604, categoria: 'bebidas', nombre: 'Cerveza Club Colombia', precio: '6.500', imagen: 'https://images.unsplash.com/photo-1535958636474-b021ee887b13?w=500', disponibilidad: 12, ingredientes: ['Malta', 'Lúpulo', 'Levadura'] },
  { id: 605, categoria: 'bebidas', nombre: 'Té Hatsu', precio: '8.000', imagen: 'https://images.unsplash.com/photo-1556679343-c7306c1976bc?w=500', disponibilidad: 12, ingredientes: ['Agua', 'Extracto de té', 'Sabor natural'] },
  { id: 606, categoria: 'bebidas', nombre: 'Agua Mineral', precio: '4.500', imagen: 'https://images.unsplash.com/photo-1548839140-29a749e1cf4d?w=500', disponibilidad: 12, ingredientes: ['Agua mineral natural'] },
  // Adicionales
  { id: 701, categoria: 'adicionales', nombre: 'Porción de Papas Fritas', precio: '7.500', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSs_mVbzoN29bqO0I1yBzzUzaKBaEsE1duegw&s', disponibilidad: 12, ingredientes: [] },
  { id: 702, categoria: 'adicionales', nombre: 'Aros de Cebolla x8', precio: '10.000', imagen: 'https://images.unsplash.com/photo-1639024471283-03518883512d?w=500', disponibilidad: 12, ingredientes: [] },
  { id: 703, categoria: 'adicionales', nombre: 'Deditos de Queso x4', precio: '12.000', imagen: 'https://cdn.colombia.com/gastronomia/2011/08/05/deditos-de-queso-1631.gif', disponibilidad: 12, ingredientes: [] },
  { id: 704, categoria: 'adicionales', nombre: 'Tocino Extra (2 lonjas)', precio: '4.500', imagen: 'https://img.freepik.com/fotos-premium/porcion-tocino-frito_846485-9752.jpg', disponibilidad: 12, ingredientes: [] },
  { id: 705, categoria: 'adicionales', nombre: 'Huevo Frito Extra', precio: '2.500', imagen: 'https://images.unsplash.com/photo-1525351484163-7529414344d8?w=500', disponibilidad: 12, ingredientes: [] },
  { id: 706, categoria: 'adicionales', nombre: 'Salsas extra x 3', precio: '3.000', imagen: 'https://www.aceitesalbert.com/wp-content/uploads/2020/06/10-mejores-salsas-del-mundo-con-Aceite-de-Oliva-Virgen-Extra.jpg', disponibilidad: 12, ingredientes: [] }
];
// 2. FILTRAMOS POR DEFECTO PARA QUE MUESTRE HAMBURGUESAS AL INICIO
const productos = ref(todosLosProductos.filter(producto => producto.categoria === 'hamburguesas'));

// 3. LA NUEVA FUNCIÓN QUE FILTRA EL ARREGLO MAESTRO
const categoriaActiva = ref('hamburguesas');

function cambiarSeccion(clave) {
  categoriaActiva.value = clave; // Guardamos cuál está seleccionada
  productos.value = todosLosProductos.filter(p => p.categoria === clave).map(p => ({ ...p, volteada: false }));
}

function voltear(comida) {
  comida.volteada = !comida.volteada;
}

// Carrito
const mostrarCarrito = ref(false);
const totalDinero = ref(0);
const carrito = ref([]);
const cantidadCarrito = ref(0);
const alertaMensaje = ref('');
const alertaVisible = ref(false);
const alertaTipo = ref('exito');
const cargando = ref(false); // Esta es la variable del spinner


function lanzarAlerta(msj, tipo) {
  alertaMensaje.value = msj;
  alertaTipo.value = tipo;
  alertaVisible.value = true;

  setTimeout(() => {
    alertaVisible.value = false;
  }, 3000);
}

function toggleCarrito() {
  mostrarCarrito.value = !mostrarCarrito.value;
}

function agregarAlcarrito(comida) {
  if (comida.disponibilidad > 0) {
    comida.disponibilidad = comida.disponibilidad - 1;
    carrito.value.push(comida);
    cantidadCarrito.value = carrito.value.length;
    const precioNumerico = parseInt(comida.precio.replace('.', ''));
    totalDinero.value += precioNumerico;
    lanzarAlerta(`¡${comida.nombre} agregado! 🍔`, 'exito');
  } else {
    lanzarAlerta('¡Producto agotado! 🚫', 'error');
  }
}

const mostrarFactura = ref(false);
const datosFactura = ref({ items: [], total: 0, fecha: '', id: 0 });

function confirmarPedido() {
  if (carrito.value.length === 0) {
    lanzarAlerta('¡Tu carrito está triste porque está vacío! 🛒', 'error');
    return;
  }
  datosFactura.value = {
    items: [...carrito.value],
    total: totalDinero.value,
    fecha: new Date().toLocaleString(),
    id: Math.floor(Math.random() * 100000)
  }
  cargando.value = true;
  setTimeout(() => {
    cargando.value = false;
    mostrarFactura.value = true;
    lanzarAlerta('¡Pedido confirmado! En camino... 🛵', 'exito');
    carrito.value = [];
    cantidadCarrito.value = 0;
    totalDinero.value = 0;
    mostrarCarrito.value = false;
  }, 2000); // 2000 milisegundos = 2 segundos
}
function eliminarDelCarrito(index) {
  const productoAEliminar = carrito.value[index];
  productoAEliminar.disponibilidad = productoAEliminar.disponibilidad + 1;
  const precioNumerico = parseInt(productoAEliminar.precio.replace('.', ''));
  totalDinero.value -= precioNumerico;
  carrito.value.splice(index, 1);
  cantidadCarrito.value = carrito.value.length;
  lanzarAlerta(`Eliminaste: ${productoAEliminar.nombre} 🗑️`, 'info');
}

function descargarPDF() {
  const elemento = document.getElementById('seccion-factura');
  const opciones = {
    margin: 1,
    filename: `Factura_BurgBite_${datosFactura.value.id}.pdf`,
    image: { type: 'jpeg', quality: 0.98 },
    html2canvas: { scale: 2 },
    jsPDF: { unit: 'in', format: 'letter', orientation: 'portrait' }
  };
  html2pdf().set(opciones).from(elemento).save();
}
</script>

<style>
/* Reset de Box Sizing para evitar desbordes */
* {
  box-sizing: border-box;
}

html,
body {
  overflow-x: hidden;
  margin: 0 !important;
  padding: 0 !important;
  width: 100% !important;
  height: 100% !important;
}

.contenedor-principal {
  background-color: #D35400;
  min-height: 100vh;
  width: 100%;
  /* <-- Ajuste clave: cambiado de 100vw a 100% para quitar el scroll horizontal */
  margin: 0;
  padding: 0;
}

.barra-superior {
  background: #3D2314;
  border-bottom: 1px solid #D35400;
  padding: 10px 0;
  position: sticky;
  top: 0;
  z-index: 10;
}

.ajuste-ancho {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.marca-tienda {
  display: flex;
  align-items: center;
  gap: 10px;
}

.icono-logo-img {
  width: 45px;
  height: 45px;
}

.titulo-negocio {
  margin: 0;
  font-size: 1.2rem;
  color: #FDFEFE;
  font-weight: 800;
}

.naranja {
  color: #f36f21;
}

.ciudad {
  margin: 0;
  font-size: 0.8rem;
  color: #888;
}

.circulo-icono {
  position: relative;
  background: #8B4513;
  padding: 10px;
  border-radius: 50%;
}

.svg-carrito {
  width: 24px;
  height: 24px;
  color: white;
}

.bolita-roja {
  position: absolute;
  top: -2px;
  right: -2px;
  background: #ed4337;
  color: white;
  font-size: 10px;
  padding: 3px 6px;
  border-radius: 10px;
  border: 2px solid white;
  transition: all 0.2s ease;
  transform: scale(1.2);
}

.selector-categorias {
  background: #FDFEFE;
  border-bottom: 1px solid #D35400;
}

.flex-categorias {
  background-color: #3D2314;
  display: flex;
  justify-content: center;
  gap: 15px;
  padding: 20px;
}

.pestaña {
  background: #E1AD01;
  border: 1px solid #ddd;
  padding: 10px 20px;
  border-radius: 25px;
  cursor: pointer;
  color: #FDFEFE;
  font-weight: 600;
  transition: 0.3s;
}

.pestaña:hover {
  border-color: #f36f21;
  color: #FDFEFE;
  background: #A30000;
}

.seccion-comida {
  max-width: 1200px;
  margin: 30px auto;
  padding: 0 20px;
}

.rejilla-productos {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 30px;
}


@media (max-width: 768px) {
  .rejilla-productos {
    grid-template-columns: repeat(1, 1fr);
  }
}

.contenedor-foto {
  background: #f4f4f4;
  height: 220px;
  overflow: hidden;
}

.foto-producto-web {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.detalles {
  padding: 25px;
  background-color: #3D2314;
}

.nombre-comida {
  margin: 0 0 10px 0;
  font-size: 1.1rem;
  color: #FDFEFE;
  font-weight: 700;
}

.precio-comida {
  font-size: 1.4rem;
  font-weight: 800;
  color: #E1AD01;
  margin-bottom: 20px;
}

.boton-comprar {
  background: #A30000;
  color: white;
  border: none;
  padding: 12px;
  width: 100%;
  border-radius: 12px;
  font-weight: bold;
  cursor: pointer;
  font-size: 1rem;
}

.boton-agotado {
  background: #666 !important;
  color: #FDFEFE !important;
  cursor: not-allowed !important;
  opacity: 0.6;
}

.stock {
  font-size: 0.85rem;
  margin-bottom: 15px;
  color: #FDFEFE;
  font-weight: 500;
}

.alerta-stock {
  color: #FF4D4D !important;
  font-weight: 800;
  text-transform: uppercase;
  animation: vibrar 0.3s ease-in-out infinite alternate;
}

@keyframes vibrar {
  from {
    transform: scale(1);
  }

  to {
    transform: scale(1.05);
  }
}

.pie-final {
  text-align: center;
  padding: 30px;
  color: white;
  background-color: #3D2314;
  font-size: 0.85rem;
}

/* 1. El contenedor con perspectiva */
.tarjeta-contenedor {
  background-color: transparent;
  perspective: 1000px;
  /* Crea el efecto 3D */
  height: 450px;
  /* Ajusta según el alto de tus cards */
}

/* 2. La caja que realmente gira */
.tarjeta-inner {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  transition: transform 0.8s cubic-bezier(0.4, 0, 0.2, 1);
  transform-style: preserve-3d;
}

/* 3. Estilo común para ambas caras */
.tarjeta-frente,
.tarjeta-atras {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  /* Oculta la cara que queda atrás */
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

/* 5. Cara trasera (ya empieza volteada) */
.tarjeta-atras {
  background-color: #3D2314;
  color: white;
  transform: rotateY(180deg);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  border: 2px solid #E1AD01;
}

.contenido-atras h3 {
  color: #E1AD01;
  margin-bottom: 15px;
}

.boton-volver {
  margin-top: 20px;
  background: transparent;
  border: 1px solid #E1AD01;
  color: white;
  padding: 8px 15px;
  border-radius: 10px;
  cursor: pointer;
}

/* Quitamos el hover anterior y usamos esta clase */
.tarjeta-inner.es-volteada {
  transform: rotateY(180deg);
}

.boton-info {
  background: transparent;
  border: 1px solid #E1AD01;
  color: #E1AD01;
  padding: 5px 10px;
  border-radius: 8px;
  margin-bottom: 10px;
  cursor: pointer;
  font-size: 0.8rem;
  transition: 0.3s;
}

.boton-info:hover {
  background: #E1AD01;
  color: #3D2314;
}

.panel-carrito {
  position: fixed;
  right: 0;
  top: 0;
  width: 350px;
  height: 100vh;
  background: #3D2314;
  z-index: 100;
  display: flex;
  flex-direction: column;
  box-shadow: -5px 0 15px rgba(0, 0, 0, 0.5);
  color: white;
}

.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.6);
  z-index: 90;
}

.encabezado-carrito {
  padding: 20px;
  background: #2D1A0F;
  border-bottom: 2px solid #D35400;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo-carrito {
  display: flex;
  align-items: center;
  gap: 10px;
}

.logo-carrito img {
  width: 35px;
}

.logo-carrito h2 {
  font-size: 1rem;
  margin: 0;
}

.lema {
  font-size: 0.7rem;
  color: #E1AD01;
  margin: 0;
}

.boton-cerrar {
  background: #A30000;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
}

.lista-items {
  flex: 1;
  overflow-y: auto;
  padding: 15px;
}

.item-carrito {
  display: flex;
  gap: 15px;
  align-items: center;
  background: rgba(255, 255, 255, 0.05);
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 10px;
}

.item-carrito img {
  width: 50px;
  height: 50px;
  border-radius: 8px;
  object-fit: cover;
}

.item-nombre {
  font-size: 0.9rem;
  font-weight: bold;
  margin: 0;
}

.item-precio {
  color: #E1AD01;
  margin: 0;
  font-size: 0.85rem;
}

.pie-carrito {
  padding: 20px;
  background: #2D1A0F;
  border-top: 1px solid #444;
}

.total-seccion {
  display: flex;
  justify-content: space-between;
  font-size: 1.1rem;
  margin-bottom: 15px;
}

.monto-total {
  color: #E1AD01;
  font-weight: 800;
}

.boton-pedir {
  width: 100%;
  padding: 15px;
  background: #D35400;
  color: white;
  border: none;
  border-radius: 10px;
  font-weight: bold;
  cursor: pointer;
  font-size: 1rem;
}

.carrito-vacio {
  color: #FDFEFE;
  font-size: 0.95rem;
  text-align: center;
  padding: 20px 0;
}

.tocar-para-volver {
  display: block;
  margin-top: 20px;
  font-size: 0.7rem;
  opacity: 0.7;
  text-transform: uppercase;
}

.pestaña-activa {
  background: #A30000 !important;
  border-color: #f36f21;
  transform: scale(1.1);
  /* Un poquito más grande */
}

.boton-eliminar {
  background: transparent;
  border: none;
  font-size: 1.2rem;
  cursor: pointer;
  padding: 5px;
  transition: transform 0.2s;
}

.boton-eliminar:hover {
  transform: scale(1.3);
}

.toast-personalizado {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  padding: 15px 25px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  gap: 12px;
  z-index: 1000;
  color: white;
  font-weight: bold;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
  animation: slideIn 0.4s ease-out;
  /* Animación de entrada */
}

.exito {
  background-color: #27ae60;
  border-bottom: 4px solid #1e8449;
}

.error {
  background-color: #A30000;
  border-bottom: 4px solid #7b0000;
}

.info {
  background-color: #2980b9;
  border-bottom: 4px solid #1f618d;
}

.icono-alerta {
  font-size: 1.2rem;
}

/* Animación de entrada */
@keyframes slideIn {
  from {
    top: -100px;
    opacity: 0;
  }

  to {
    top: 20px;
    opacity: 1;
  }
}

/* El resto del CSS (tarjeta-contenedor, tarjeta-frente, etc.) se mantiene igual */

.pantalla-carga {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 2000;
  /* Por encima de todo */
  color: white;
}

/* El círculo que gira */
.spinner {
  width: 50px;
  height: 50px;
  border: 5px solid rgba(255, 255, 255, 0.1);
  border-top: 5px solid #E1AD01;
  /* El amarillo de tu marca */
  border-radius: 50%;
  animation: girar 1s linear infinite;
  margin-bottom: 20px;
}

@keyframes girar {
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
}

.overlay-factura {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 3000;
}

.card-factura {
  background: white;
  color: #333;
  width: 90%;
  max-width: 450px;
  max-height: 85vh;
  padding: 30px;
  border-radius: 15px;
  position: relative;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
  display: flex;
  flex-direction: column;
  overflow-y: auto;
}

.contenido-factura {
  padding: 10px;
  flex: 1;
  overflow-y: auto;
}

.header-factura {
  text-align: center;
  margin-bottom: 20px;
}

.header-factura img {
  width: 60px;
}

.header-factura h1 {
  color: #3D2314;
  margin: 5px 0;
}

.header-factura .naranja {
  color: #D35400;
}

.info-pedido {
  font-size: 0.9rem;
  margin-bottom: 20px;
  line-height: 1.5;
}

.tabla-factura {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.tabla-factura th {
  text-align: left;
  border-bottom: 2px solid #D35400;
  padding: 10px;
}

.tabla-factura td {
  padding: 10px;
  border-bottom: 1px solid #eee;
}

.total-factura {
  text-align: right;
  margin-top: 20px;
  color: #D35400;
}

.gracias {
  text-align: center;
  font-style: italic;
  margin-top: 20px;
  font-size: 0.8rem;
}

.boton-descargar {
  width: 100%;
  padding: 12px;
  background: #27ae60;
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
  margin-top: 15px;
}

.cerrar-factura {
  position: absolute;
  top: 10px;
  right: 15px;
  background: #A30000;
  color: white;
  border: none;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  cursor: pointer;
}
</style>
