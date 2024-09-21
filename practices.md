# Guía de Workflow para Proyectos Go con Docker

## Configuración Inicial del Proyecto

1. Crear un nuevo directorio para el proyecto:
   ```bash
   mkdir mi_proyecto_go
   cd mi_proyecto_go
   ```

2. Inicializar el módulo Go:
   ```bash
   go mod init mi_proyecto_go
   ```

3. Crear el archivo principal `main.go`:
   ```bash
   touch main.go
   ```

4. Editar `main.go` con tu código (ejemplo usando Fiber):
   ```go
   package main

   import (
       "fmt"
       "log"

       "github.com/gofiber/fiber/v2"
   )

   func main() {
       app := fiber.New()

       app.Get("/", func(c *fiber.Ctx) error {
           return c.SendString("¡Hola, mundo!")
       })

       fmt.Println("Servidor iniciando en http://localhost:8080")
       log.Fatal(app.Listen(":8080"))
   }
   ```

## Gestión de Dependencias

5. Añadir dependencias (siempre en tu máquina local, no en el contenedor):
   ```bash
   go get -u github.com/gofiber/fiber/v2
   ```

6. Verificar que `go.mod` y `go.sum` se han actualizado correctamente.

## Configuración de Docker

7. Crear un `Dockerfile` en el directorio del proyecto:
   ```dockerfile
   # Usa una imagen base de Go
   FROM golang:1.21-alpine

   # Establece el directorio de trabajo
   WORKDIR /app

   # Copia go.mod y go.sum
   COPY go.mod go.sum ./

   # Descarga las dependencias
   RUN go mod download

   # Copia el resto del código fuente
   COPY . .

   # Compila la aplicación
   RUN go build -o main .

   # Expone el puerto
   EXPOSE 8080

   # Ejecuta la aplicación
   CMD ["./main"]
   ```

## Construcción y Ejecución

8. Construir la imagen Docker:
   ```bash
   docker build -t mi_proyecto_go .
   ```

9. Ejecutar el contenedor:
   ```bash
   docker run -d -p 8080:8080 mi_proyecto_go
   ```

## Desarrollo Iterativo

10. Para hacer cambios:
    - Edita los archivos en tu máquina local
    - Si añades nuevas dependencias, usa `go get` en tu máquina local
    - Reconstruye la imagen Docker
    - Detén el contenedor anterior y ejecuta uno nuevo

## Comandos Útiles de Docker

- Ver contenedores en ejecución:
  ```bash
  docker ps
  ```

- Detener un contenedor:
  ```bash
  docker stop <container_id>
  ```

- Ver logs de un contenedor:
  ```bash
  docker logs <container_id>
  ```

## Notas Importantes

- Siempre gestiona las dependencias en tu máquina local, no dentro del contenedor.
- Asegúrate de que `go.mod` y `go.sum` estén actualizados antes de construir la imagen Docker.
- No ejecutes `go get` dentro del contenedor en ejecución, ya que los cambios se perderán.
- Si necesitas un shell dentro del contenedor (solo para depuración):
  ```bash
  docker exec -it <container_id> /bin/sh
  ```
  Recuerda que los cambios aquí son temporales y no afectan a tu código local o a la imagen.
