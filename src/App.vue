<template>
  <div>
    <input type="file" @change="handleFileUpload" accept=".csv" />
    <button @click="enviarDatos">Enviar Datos</button>
  </div>
</template>

<script>
import Papa from 'papaparse';

export default {
  data() {
    return {
      csvFile: null,
      jsonData: null,
      url: 'https://8j5baasof2.execute-api.us-west-2.amazonaws.com/production/tests/trucode/items', // Reemplaza con tu URL real
    };
  },
  methods: {
    handleFileUpload(event) {
      const file = event.target.files[0];
      if (file) {
        Papa.parse(file, {
          header: false, // El archivo CSV no tiene encabezados
          dynamicTyping: true, // Intenta convertir automáticamente los valores en números, booleanos, etc.
          skipEmptyLines: true, // Omite las líneas vacías
          complete: (result) => {
            // Convierte cada fila en un objeto JSON con las claves "nombre", "numero" y "email"
            this.jsonData = result.data.map((row) => {
              const [name, phone, email] = row[0].split(',');

              // Formatea el número agregando un guion medio cada 3 caracteres
              const numeroFormateado = phone.replace(/(\d{3})(\d{3})(\d{4})/, '$1-$2-$3');

              return { name, phone: numeroFormateado, email };
            });
            // console.log(this.jsonData[1]);
          },
          error: (error) => {
            console.error('Error al procesar el archivo CSV:', error);
          },
        });
      }
    },
    enviarDatos() {
      if (this.jsonData.length === 0) {
        console.error('No hay datos en jsonData.');
        return;
      }

      const url = 'https://8j5baasof2.execute-api.us-west-2.amazonaws.com/production/tests/trucode/items'; // Reemplaza con tu URL real

      // Itera a través de cada fila en jsonData
      this.jsonData.forEach((fila, index) => {
        // Formatea el número agregando un guion medio cada 3 caracteres
        if (fila.phone) {
          fila.phone = fila.phone.replace(/(\d{3})(\d{3})(\d{4})/, '$1-$2-$3');
        }
        console.log(fila);
        fetch(url, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(fila),
        })
          .then((response) => {
            if (!response.ok) {
              throw new Error(`La solicitud para fila ${index + 1} ha fallado`);
            }
            return response.json();
          })
          .then((data) => {
            console.log(`Solicitud exitosa para fila ${index + 1}:`, data);
            // Realiza acciones adicionales si es necesario
          })
          .catch((error) => {
            console.error(`Error para fila ${index + 1}:`, error);
            // Maneja el error de la solicitud
          });
      });
    },
  },
};
</script>