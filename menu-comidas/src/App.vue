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

        <div class="boton-carrito">
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
        <button v-for="cat in listaCategorias" :key="cat.id" class="pestaña" @click="cambiarSeccion(cat.clave)">
          {{ cat.nombre }}
        </button>
      </div>
    </nav>

    <main class="seccion-comida">
      <div class="rejilla-productos">

        <div v-for="comida in productos" :key="comida.id" class="tarjeta">
          <div class="contenedor-foto">
            <img :src="comida.imagen" :alt="comida.nombre" class="foto-producto-web">
          </div>

          <div class="detalles">
            <h3 class="nombre-comida">{{ comida.nombre }}</h3>
            <p class="precio-comida">${{ comida.precio }}</p>
            <button class="boton-comprar">Agregar al carrito</button>
          </div>
        </div>

      </div>
    </main>

    <footer class="pie-final">
      <p>Desarrollado para Burg & Bite - 2026</p>
    </footer>

  </div>
</template>

<script setup>
import { ref } from 'vue';

const cantidadCarrito = ref(2);

const listaCategorias = ref([
  { id: 1, nombre: 'Hamburguesas', clave: 'hamburguesas' },
  { id: 2, nombre: 'Hotdogs', clave: 'hotdogs' },
  { id: 3, nombre: 'Pizzas', clave: 'pizzas' },
  { id: 4, nombre: 'Salchipapas', clave: 'salchipapas' },
  { id: 5, nombre: 'Postres', clave: 'postres' },
  { id: 6, nombre: 'Bebidas', clave: 'bebidas' },
  { id: 7, nombre: 'Adicionales', clave: 'adicionales' }
]);

const baseDeDatos = {
  hamburguesas: [
    { id: 101, nombre: 'Doble Queso XL', precio: '25.900', imagen: 'https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=500' },
    { id: 102, nombre: 'Clásica Burg', precio: '18.500', imagen: 'https://images.unsplash.com/photo-1594212699903-ec8a3eca50f5?w=500' },
    { id: 103, nombre: 'Mexicana Spicy', precio: '24.900', imagen: 'https://images.unsplash.com/photo-1586190848861-99aa4a171e90?w=500' },
    { id: 104, nombre: 'Pollo Crispy', precio: '21.000', imagen: 'https://static.wixstatic.com/media/29cc8e_d8a25a27f2f640558f6723144341d662~mv2.jpg/v1/fill/w_740,h_493,al_c,q_85,usm_0.66_1.00_0.01,enc_avif,quality_auto/29cc8e_d8a25a27f2f640558f6723144341d662~mv2.jpg' },
    { id: 105, nombre: 'Triple Carne', precio: '32.000', imagen: 'https://images.unsplash.com/photo-1553979459-d2229ba7433b?w=500' },
    { id: 106, nombre: 'Veggie Delicia', precio: '22.500', imagen: 'https://images.unsplash.com/photo-1550547660-d9450f859349?w=500' }
  ],
  hotdogs: [
    { id: 201, nombre: 'Perro Sencillo', precio: '12.000', imagen: 'https://images.unsplash.com/photo-1541214113241-21578d2d9b62?w=500' },
    { id: 202, nombre: 'Perro con Todo', precio: '15.500', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRE_PEx-YRJA0XpZ9pF47f7NqroIueFnKNt9Q&s' },
    { id: 203, nombre: 'Perro Suizo', precio: '18.900', imagen: 'https://tofuu.getjusto.com/orioneat-local/resized2/D65379QMuyRwuZLyt-x-800.webp' },
    { id: 204, nombre: 'Perro Ranchero', precio: '16.000', imagen: 'https://images.rappi.com/restaurants_background/colosperrosdelmono-1675777013143.jpg' },
    { id: 205, nombre: 'Mega Perro Bite', precio: '22.000', imagen: 'https://images.rappi.com/restaurants_background/portada-1683125522549.jpg' },
    { id: 206, nombre: 'Perro Kids', precio: '10.500', imagen: 'https://images.unsplash.com/photo-1599599810694-b5b37304c041?w=500' }
  ],
  pizzas: [
    { id: 301, nombre: 'Pepperoni Clásica', precio: '35.000', imagen: 'https://images.unsplash.com/photo-1628840042765-356cda07504e?w=500' },
    { id: 302, nombre: 'Hawaiana Especial', precio: '32.500', imagen: 'https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?w=500' },
    { id: 303, nombre: 'Pollo y Champiñones', precio: '34.900', imagen: 'https://images.unsplash.com/photo-1513104890138-7c749659a591?w=500' },
    { id: 304, nombre: 'Carnívora Premium', precio: '42.000', imagen: 'https://tofuu.getjusto.com/orioneat-local/resized2/7Lma3qmnxQnvcyovr-2400-x.webp' },
    { id: 305, nombre: 'Vegetariana Huerto', precio: '29.900', imagen: 'https://images.unsplash.com/photo-1571407970349-bc81e7e96d47?w=500' },
    { id: 306, nombre: 'Mexicana con Nachos', precio: '38.500', imagen: 'https://img.freepik.com/fotos-premium/pizza-mexicana-mozzarella-cebolla-pepperoni-aceituna-negra-pimientos-verdes-nachos-oregano-pizza-mexicana-vista-superior_311876-471.jpg' }
  ],
  salchipapas: [
    { id: 401, nombre: 'Senci-Papa', precio: '14.000', imagen: 'https://imag.bonviveur.com/emplatado-final-de-las-salchipapas.jpg' },
    { id: 402, nombre: 'Salchi-Especial', precio: '19.500', imagen: 'https://peru.info/cms/files/publicacion/publicacion_20241115_123829.jpg' },
    { id: 403, nombre: 'Costeña Power', precio: '24.000', imagen: 'https://tb-static.uber.com/prod/image-proc/processed_images/fdeac4399e5a9b47d190a99a672edeb3/bc9c318a9c96996e2d990faf2b0c65f6.jpeg' },
    { id: 404, nombre: 'Criolla Tradición', precio: '18.000', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTaF0dMQK1sgfC_22Bxr9YyrFHUhaQoT_CXrQ&s' },
    { id: 405, nombre: 'Burg & Bite Mix', precio: '28.500', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSzQ_byQy1rF3XU_nNNGRkSKXruaqQ8vIyvNQ&s' },
    { id: 406, nombre: 'Salchi-Infantil', precio: '12.000', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS6aPXulQpr9vfvC6v97Ec8HSlNSHfsWMlGFA&s' }
  ],
  postres: [
  { id: 501, nombre: 'Brownie con Helado', precio: '12.500', imagen: 'https://images.unsplash.com/photo-1606313564200-e75d5e30476c?w=500' },
  { id: 502, nombre: 'Cheesecake Frutos Rojos', precio: '14.000', imagen: 'https://images.unsplash.com/photo-1533134242443-d4fd215305ad?w=500' },
  { id: 503, nombre: 'Waffle Nutella', precio: '15.900', imagen: 'https://images.unsplash.com/photo-1519915028121-7d3463d20b13?w=500' },
  { id: 504, nombre: 'Malteada de Oreo', precio: '13.500', imagen: 'https://images.unsplash.com/photo-1572490122747-3968b75cc699?w=500' },
  { id: 505, nombre: 'Pie de Limón', precio: '10.000', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQEOadD1Mhm6DZkxmy9AYdCIQ6XrFSoyUryWw&s' },
  { id: 506, nombre: 'Copa de Helado Mix', precio: '9.500', imagen: 'https://images.unsplash.com/photo-1563805042-7684c019e1cb?w=500' }
],
bebidas: [
  { id: 601, nombre: 'Coca-Cola 1.5L', precio: '8.500', imagen: 'https://licoresjunior.com/wp-content/uploads/2023/12/1.5-LITROS.jpg' },
  { id: 602, nombre: 'Limonada Natural', precio: '7.000', imagen: 'https://images.unsplash.com/photo-1513558161293-cdaf765ed2fd?w=500' },
  { id: 603, nombre: 'Jugo de Mora', precio: '7.500', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTKiKz8HjM460TIEPFJpkRvRu1ydydQf0kSsg&s' },
  { id: 604, nombre: 'Cerveza Club Colombia', precio: '6.500', imagen: 'https://images.unsplash.com/photo-1535958636474-b021ee887b13?w=500' },
  { id: 605, nombre: 'Té Hatsu', precio: '8.000', imagen: 'https://images.unsplash.com/photo-1556679343-c7306c1976bc?w=500' },
  { id: 606, nombre: 'Agua Mineral', precio: '4.500', imagen: 'https://images.unsplash.com/photo-1548839140-29a749e1cf4d?w=500' }
],
adicionales: [
  { id: 701, nombre: 'Porción de Papas Fritas', precio: '7.500', imagen: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSs_mVbzoN29bqO0I1yBzzUzaKBaEsE1duegw&s' },
  { id: 702, nombre: 'Aros de Cebolla x8', precio: '10.000', imagen: 'https://images.unsplash.com/photo-1639024471283-03518883512d?w=500' },
  { id: 703, nombre: 'Deditos de Queso x4', precio: '12.000', imagen: 'https://www.campi.com.co/wp-content/uploads/2021/03/Deditos-De-Queso-Imagen-Destacada.jpg' },
  { id: 704, nombre: 'Tocino Extra (2 lonjas)', precio: '4.500', imagen: 'https://img.freepik.com/fotos-premium/porcion-tocino-frito_846485-9752.jpg' },
  { id: 705, nombre: 'Huevo Frito Extra', precio: '2.500', imagen: 'https://images.unsplash.com/photo-1525351484163-7529414344d8?w=500' },
  { id: 706, nombre: 'Salsas extra x 3', precio: '3.000', imagen: 'https://www.aceitesalbert.com/wp-content/uploads/2020/06/10-mejores-salsas-del-mundo-con-Aceite-de-Oliva-Virgen-Extra.jpg' }
]
};

const productos = ref(baseDeDatos.hamburguesas);

function cambiarSeccion(clave) {
  if (baseDeDatos[clave]) {
    productos.value = baseDeDatos[clave];
  }
}
</script>

<style>
html, body {
  margin: 0 !important;
  padding: 0 !important;
  width: 100% !important;
  height: 100% !important;
}
.contenedor-principal {
background-color: #D35400;
  min-height: 100vh;
  width: 100vw; /* Asegura que ocupe todo el ancho de la ventana */
  height: 100%;
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

/* AJUSTE DE LA REJILLA 3x2 */
.seccion-comida {
  max-width: 1200px;
  /* Un poco más ancho para que quepan bien las 3 columnas */
  margin: 30px auto;
  padding: 0 20px;
}

.rejilla-productos {
  display: grid;
  /* FORZAMOS 3 COLUMNAS EXACTAS EN PANTALLAS GRANDES */
  grid-template-columns: repeat(3, 1fr);
  gap: 30px;
  /* Más espacio entre tarjetas */
}

/* RESPONSIVE: Si la pantalla es pequeña (celular), bajamos a 1 columna */
@media (max-width: 768px) {
  .rejilla-productos {
    grid-template-columns: repeat(1, 1fr);
  }
}

.tarjeta {
  background: white;
  border-radius: 20px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.08);
  overflow: hidden;
  text-align: center;
  transition: transform 0.3s;
}

.tarjeta:hover {
  transform: translateY(-10px);
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

.pie-final {
  text-align: center;
  padding: 30px;
  color: white;
  background-color: #3D2314;
  font-size: 0.85rem;
}
</style>