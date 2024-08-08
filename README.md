# Sistema de Spawn

# Indice

- Introducción
- Códigos del sistema
- Detalles a tomar en cuenta
- Importancia de los Colisionadores
- Personalización
- Conclusión

# Introducción

El sistema de spawn es una funcionalidad muy importante en los juegos, ya que permite a los jugadores aparecer en un lugar específico al inicio del juego o después de morir. Asimismo puede ser usado para aparecer ciertos objetos y enemigos en el campo con los que el jugador puede interactuar.

Existen varios tipos de sistemas de spawn, como el spawn aleatorio, en el que se aparece en un lugar aleatorio del mapa, o el spawn fijo, donde se aparece siempre en el mismo lugar. También hay sistemas de spawn dinámicos, en los que el lugar de aparición cambia en función de ciertas condiciones del juego.

Para implementar un sistema de spawn, es necesario tener en cuenta varios factores. En primer lugar, es importante definir los puntos de spawn y determinar las condiciones para que se active cada uno. También es necesario tener en cuenta el sistema de colisión del juego, para evitar que los jugadores aparezcan en lugares inapropiados o dentro de objetos.

Además, el sistema de spawn debe ser lo suficientemente flexible como para permitir una fácil modificación y personalización por parte de los desarrolladores del juego. Esto incluye la posibilidad de agregar nuevos puntos de spawn, cambiar las condiciones de activación y modificar la lógica del sistema.

En resumen, un buen sistema de spawn es esencial para asegurar una experiencia de juego fluida y justa para los jugadores. Al implementar un sistema de spawn, es importante tener en cuenta los diferentes tipos de spawn y las condiciones necesarias para cada uno, así como la flexibilidad y personalización del sistema.

# Códigos del sistema

# Cambio de Escena

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class CambioEscena : MonoBehaviour
{

public void SceneChange(int ewe)
{
        SceneManager.LoadScene(ewe);
}

    public void ExitScene()
    {
       Application.Quit();
    }

}

```

Este código se encarga de manejar la transición de escenas usando botones como ejecutantes.

# Contador Destrucciones

```csharp
using UnityEngine;
using TMPro;

public class ContadorDeDestrucciones : MonoBehaviour
{
    public TextMeshProUGUI textoContador;
    private int conteoDestrucciones = 0;

    private void Start()
    {
        ActualizarTextoContador();
    }

    public void IncrementarConteo()
    {
        conteoDestrucciones++;
        ActualizarTextoContador();
    }

    private void ActualizarTextoContador()
    {
        textoContador.text = "Objetos Destruidos: " + conteoDestrucciones;
    }
}
```

Este código se encarga de llevar un conteo en tiempo real de cuantos objetos han sido destruidos por el jugador.

# Spawn and Destroy

```csharp
using System.Collections;
using UnityEngine;

public class GeneradorDeObjetos : MonoBehaviour
{
    public GameObject objetoAGenerar;
    public float intervaloMin = 1f;
    public float intervaloMax = 5f;

    private void Start()
    {
        StartCoroutine(GenerarObjeto());
    }

    private IEnumerator GenerarObjeto()
    {
        while (true)
        {
            // Espera un intervalo aleatorio entre intervaloMin y intervaloMax
            float intervalo = Random.Range(intervaloMin, intervaloMax);
            yield return new WaitForSeconds(intervalo);

            // Genera el objeto en la posición del generador
            GameObject objeto = Instantiate(objetoAGenerar, transform.position, transform.rotation);

            // Espera 2 segundos antes de destruir el objeto
            Destroy(objeto, 2f);
        }
    }
}
```

Este código se encarga de reaparecer los blancos a destruir en un intervalo de 5 segundos a partir de que inicia el juego. Adicionalmente se encarga de destruir los objetos no destruidos por el jugador en caso de permanecer así mas de 3 segundos con el fin de no saturar el escenario

# Shoot Proyectile

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using TMPro; // Asegúrate de tener esto para usar TextMeshPro

public class ShootProyectile : MonoBehaviour
{
    public GameObject projectilePrefab; // Prefab del proyectil
    public float projectileSpeed = 20f; // Velocidad del proyectil
    public Transform firePoint; // Punto desde donde se disparará el proyectil

    public int maxAmmo = 10; // Munición máxima
    private int currentAmmo; // Munición actual
    private bool isReloading = false; // Indicador de si se está recargando

    public float reloadTime = 2f; // Tiempo de recarga
    public float fireRate = 0.5f; // Tiempo entre disparos
    private float nextTimeToFire = 0f; // Tiempo hasta que se puede disparar de nuevo
    public TextMeshProUGUI ammoText; // Texto de la UI para mostrar la munición restante

    void Start()
    {
        currentAmmo = maxAmmo; // Inicializar la munición actual
        UpdateAmmoUI(); // Actualizar la UI de la munición
    }

    void Update()
    {
        // Disparar cuando se presiona el botón de disparo (por ejemplo, el botón izquierdo del ratón) y si no se está recargando
        if (Input.GetButtonDown("Fire1") && !isReloading && Time.time >= nextTimeToFire)
        {
            nextTimeToFire = Time.time + fireRate;
            Shoot();
        }

        // Recargar cuando se presiona el botón de recarga (por ejemplo, la tecla 'R')
        if (Input.GetKeyDown(KeyCode.R) && !isReloading && currentAmmo < maxAmmo)
        {
            StartCoroutine(Reload());
        }
    }

    public void Shoot()
    {
        if (currentAmmo > 0)
        {
            // Instanciar el proyectil en la posición y rotación del punto de disparo
            GameObject projectile = Instantiate(projectilePrefab, firePoint.position, firePoint.rotation);
            Rigidbody rb = projectile.GetComponent<Rigidbody>();
            if (rb != null)
            {
                rb.velocity = firePoint.forward * projectileSpeed; // Aplicar velocidad al proyectil
            }

            currentAmmo--; // Disminuir la munición actual
            UpdateAmmoUI(); // Actualizar la UI de la munición
        }
        else
        {
            // Si no hay munición, iniciar la recarga automáticamente
            if (!isReloading)
            {
                StartCoroutine(Reload());
            }
        }
    }

    IEnumerator Reload()
    {
        isReloading = true;
        // Opcional: Añadir alguna animación o sonido de recarga aquí

        yield return new WaitForSeconds(reloadTime);

        currentAmmo = maxAmmo; //recargar munición
        isReloading = false;
        UpdateAmmoUI(); //actualizar UI munición
    }

    void UpdateAmmoUI()
    {
        ammoText.text = "Munición: " + currentAmmo + " / " + maxAmmo;
    }
}

```

Este código tiene 3 funciones:

Manejar al proyectil: Crea proyectiles desde la AR Camera usando el botón de la UI para llamar a la función, la velocidad y diámetro del proyectil pueden ser editados desde el editor de unity.

Contar y Reponer Munición: Se lleva la cuenta de la munición total de la cámara, esta por defecto es de 10 proyectiles; al llegar a 0 proyectiles se entra en estado de recarga por 5 segundos y pasados estos se repone la munición, durante este tiempo no se pueden disparar nuevos proyectiles.

Unir la cámara AR al proyectil: La función principal de este código es anexar el objeto firepoint del editor a la AR camera para que sirva como punto de disparo en primera persona.

# Proyectile

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Proyectile : MonoBehaviour
{
    public float lifeTime = 5f; // Tiempo de vida del proyectil
    private ContadorDeDestrucciones contadorDeDestrucciones; // Referencia al contador de destrucciones

    void Start()
    {
        // Buscar y asignar el script ContadorDeDestrucciones en la escena
        contadorDeDestrucciones = FindObjectOfType<ContadorDeDestrucciones>();

        // Destruir el proyectil después de un tiempo para evitar acumular muchos en la escena
        Destroy(gameObject, lifeTime);
    }

    void OnCollisionEnter(Collision collision)
    {
        // Destruir el objeto con el que colisiona, si tiene un componente Collider
        if (collision.gameObject.CompareTag("Destructible"))
        {
            Destroy(collision.gameObject);
            if (contadorDeDestrucciones != null)
            {
                contadorDeDestrucciones.IncrementarConteo(); // Incrementar el contador
            }
        }
        // Destruir el proyectil después de la colisión
        Destroy(gameObject);
    }
}
```

El código se encarga de que todas las instancias de los proyectiles al entrar en

# Detalles a tomar en cuenta

Es importante tomar en cuenta que para que el juego funcione correctamente hay que tener la cámara viendo en todo el momento el objetivo de track para así tener una experiencia de juego mas estable.

# Importancia de los Colisionadores

Los colisionadores son una parte esencial de este juego, ya que permiten que el jugador interactúe de manera activa con todos los objetos del mapa. Es importante asegurarse de que los colisionadores estén configurados correctamente y sean lo suficientemente precisos para evitar problemas de colisión. Además, es recomendable utilizar un sistema de colisión dinámico que pueda detectar cambios en el entorno de juego. Asimismo es necesario tenerlos configurados correctamente ya que de no estarlo los scripts anexados a los objetos con colisionadores podrían no funcionar adecuadamente. Si deseas hacer alguna modificación a algún componente de la escena es importante que mantengas la configuración de colisionador lo más limpia posible para evitar errores.

# Conclusión

En resumen, el sistema de spawn es un componente vital en cualquier juego. Para implementarlo adecuadamente, es necesario tener en cuenta los diferentes tipos de spawn y las condiciones necesarias para cada uno, así como la flexibilidad y personalización del sistema. También es importante prestar atención al sistema de colisión del juego y asegurarse de que los colisionadores estén configurados correctamente. Al seguir estos consejos, se puede crear un sistema de spawn eficiente y personalizable que mejore la experiencia de juego para los jugadores.