# Telegram Dump History

+ Interfaz de línea de comandos para [Telegram](http://telegram.org).
+ Ingesta de datos para el procesamiento con Machine Learning; el poryecto descarga en formato CSV,JSON y JSONL el historial de conversaciones privadas, grupos y canales a los que el usuario está suscrito. 

### Documentación del API y el protocolo

+ La documentación del APi de Telegram está disponible aquí: http://core.telegram.org/api

+ La documentación del protocolo MTproto está disponible aquí: http://core.telegram.org/mtproto
### Requisitos

+ Ubuntu 16.04 (64-bits)

---

### Estructura

![N|estructura](https://i.ibb.co/MML2Dc3/Estructura-Telegram.png)

---

### Instalación

Clona el Repositorio GitHub

    $ git clone https://github.com/bisite/Telegram-History-Dump.git && cd Telegram-History-Dump/telegram
    
Descomprime el zip que contiene el proyecto y elimina el archivo comprimido

    $ unzip Telegram_Machine_Learning.zip && rm Telegram_Machine_Learning.zip
    
Ejecutamos el fichero __instalation. sh__ con permisos __sudo__ el cual instalará las bibliotecas necesarias para el correcto funcionamiento del proyecto y se encargará de compilarlo automáticamente.

    $ sudo sh instalation.sh

---

### Ejecución del proyecto

Ejecutamos el fihero __api. py__ con el que conseguimos controlar las acciones del proyecto mediante los endpoints que crea.

    $ python api.py
    
| Endpoints | Descripción |
| ------ | ------ |
| */start* | **Comprueba** si la ruta de directorios de descarga existe. **Si no existe** crea una nueva ruta de directorios de descarga y lanza el **cliente** Telegram. **Si existe**, lanza el **cliente** Telegram.|
| */stop* | **Finaliza el proceso** creado por el **cliente** Telegram|
| */dump* | Lanza **telegram-history-dump.rb**. Descarga el historial de conversaciones existentes de grupos, canales y conversaciones privadas en formato **JSONL**. Lanza **j_to_c.py**, que convierte los archivos descargados a formato **CSV** y **JSON** para poder ser tratados con **Machine Learning**. Previamente se debe haber iniciado el **cliente** Telegram (/start)|
| */new_user* | Lanza el **cliente** Telegram nuevo, permitiendo **añadir por terminal el teléfono** al que se asociará la aplicación. |
| */dialog_list* | **Muestra** por terminal, información acerca de los **dialogos y conversaciones existentes** |
| */channel_list* | **Muestra** por terminal un listado de los **canales a los que estamos suscritos** |
| */contact_list* | **Muestra** por terminal un listado de los **contactos** del usuario|

---

### Configuraciones de volcado de datos

Las configuraciones de las opciones disponibles del volcado de datos se realizan desde el fichero **config.yaml** que se encuentra en la ruta ***/Telegram/Telegram_Machine_Learning/Telegram-history-dump***

```yaml

  ## Backup targets ##
  #
  # Array of strings for each category
  # Strings can be either a (partial) name or a telegram-cli "peer_id" value
  # An empty array means "everything in this category"
  # An array containing only null means "nothing in this category"

  # Array of user dialogs to backup
  backup_users: [

  ]

  # Array of group dialogs to backup
  backup_groups: [

  ]

  # Array of supergroup dialogs to backup
  backup_supergroups: [
    
  ]

  # Array of broadcast channels to backup
  # WARNING: using this can cause a crash when a channel has too many messages
  # to fit in a single chunk (see https://github.com/vysheng/tg/issues/947)
  backup_channels: [

  ]

  # Array of (partial) names / peer IDs *not* to backup even if they match
  # any of the above target selectors
  blacklist: [

  ]

  # By default telegram-cli returns only the first 100 dialogs/channels
  # We increase this to 1000. If you have even more, increase this value
  maximum_dialogs: 1000


  ## Output options ##

  # Formatters export the JSON output to a different format
  # To enable a formatter, uncomment it along with all of its options
  # If no formatters are enabled, only JSON files are produced
  formatters: {
  #  plaintext: {
  #    date_format: '%Y-%m-%d %H:%M:%S'
  #  },
  #  bare: {},
  #  pisg: {},
  #  html: {
  #    paginate: 1500, # messages per page
  #    use_utc_time: false,
  #    timestamps_every: 50, # messages
  #    loop_video: true, # loop and autoplay videos
  #  },
  }

  # Target directory for the backup files
  # If this is a relative path it will be relative to the script's directory
  # Absolute path starts with '/' or '~'
  backup_dir: '~/Telegram-History-Dump/telegram/output'

  # Data file naming style:
  # True for "My_Chat_Name.jsonl", false for "193044.jsonl" (fixed id)
  friendly_data_filenames: true

  # True to rename data files when their name changes
  # For example, "My_Chat_Name.jsonl" is renamed to "WhoChangedTheTitle.jsonl"
  update_data_filenames: true

  # Maximum number of messages to backup for each target (0 means unlimited)
  backlog_limit: 0

  # At the time of writing, telegram-cli does not properly detect audio/video
  # These will be recognized as documents instead
  # Stickers are also considered documents
  download_media: {
    photo: true,
    document: true,
    audio: true,
    video: true,
  }

  # False to reference downloaded files in the telegram-cli cache
  # True to copy downloaded media to a dedicated directory per dialog
  copy_media: true

  # True to delete media files from telegram-cli cache right after downloading
  # Effectively turns copy_media into move_media
  # Beware: this will cause media files to be redownloaded every backup run
  delete_media: false

  # When a user's real name is formatted, include their last name if possible
  display_last_name: true


  ## Technical options ##

  # Use a unix socket to communicate with telegram-cli
  # Should match the path passed to telegram-cli with the -S option
  #
  # NB: Setting tg_sock to some not false value will cause the script
  # to ignore the next options about TCP connection

  # tg_sock: "/var/run/telegram.sock"
  tg_sock: null

  # Address of the machine you are running telegram-cli on
  # If this is not localhost telegram-cli must be invoked with --accept-any-tcp
  tg_host: 'localhost'

  # Should match the port passed to telegram-cli with the -P option
  tg_port: 9009

  # Number of messages to request in one chunk
  chunk_size: 100

  # Time in seconds to wait between requesting chunks
  chunk_delay: 1.0

  # Time in seconds to wait before considering a request stuck (0 = infinite)
  chunk_timeout: 10

  # Number of times to retry getting one chunk
  chunk_retry: 3

  # Time in seconds to wait before considering a download stuck (0 = infinite)
  # After this time the script skips to the next message
  media_timeout: 100


  ## Advanced behavior settings ##

  # Enables incremental backups (progress is tracked in <outdir>/progress.json)
  # Setting this to false will force a complete re-run of the backup every time
  track_progress: true

  # Replacement string for the characters in dialog names which are potentially
  # problematic in filenames
  # You can set this to '' if the dialog names are unique enough
  character_substitute: '_'

  # Skip messages if their text matches this Ruby regex
  # Example: '/forbidden|words/i'
  filter_regex: null

```

---

### Comandos soportados por terminal

#### Mensajería

* **msg** \<peer\> texto - envía el mensaje a este usuario.
* **fwd** \<usuario\> \<numero-mensaje\> - reenviar un mensaje al usuario. Puedes ver los número de mensajes iniciando el Cliente con -N.
* **chat_with_peer** \<peer\> - inicia un chat con este usuario. /exit o /quit para salir de este modo.
* **add_contact** \<numero-teléfono\> \<nombre\> \<apellido\> - intenta añadir este contacto a la lista de contactos.
* **rename_contact** \<usuario\> \<nombre\> \<apellido\> - intenta renombrar el contacto. Si tienes otro dispositivo será una pelea.
* **mark_read** \<peer\> - marca todos los mensajes como recibidos de ese usuario.

#### Multimedia

* **send_photo** \<peer\> \<nombre-archivo-foto\> - manda una foto al usuario.
* **send_video** \<peer\> \<nombre-archivo-video\> - envia un video al usuario.
* **send_text** \<peer\> \<nombre-archivo-texto> - envia un archivo de texto como un mensaje en plano.
* **load_photo**/load_video/load_video_thumb \<numero-mensaje\> - carga foto/video indicado del directorio de descarga.
* **view_photo**/view_video/view_video_thumb \<numero-mensaje\> - carga foto/video indicado del directorio de descarga y lo abre con el visor por defecto del sistema.


#### Opciones de chat de grupo

* **chat_info** \<chat\> - imprime información del chat.
* **chat_add_user** \<chat\> \<usuario\> - agrega un usuario al chat.
* **chat_del_user** \<chat\> \<usuario\> - elimina un usuario del chat.
* **rename_chat** \<chat\> \<nuevo-nombre\> - cambia el nombre al chat.

#### Search

* **search** \<peer\> patrón - busca el patrón indicado en los mensajes con ese usuario.
* **global_search** patrón - busca el patrón indicado en todos los mensajes.

#### Chat secreto

* **create_secret_chat** \<user\> - crea un chat secreto con el usuario indicado.
* **visualize_key** \<secret_chat\> - Muestra la clave de cifrado. Debes compararla con la del otro usuario.

#### Estadísticas e información varia.

* **user_info** \<user\> - muestra información sobre el usuario.
* **history** \<peer\> [limit] - muestra el historial (y la marca como leído). Limite por defecto = 40.
* **dialog_list** - muestra información acerca del dialogo
* **contact_list** - muestra información acerca de tu lista de contactos.
* **suggested_contacts** - muestra información sobre sus contactos, tiene un máximo de amigos comunes.
* **stats** - solo para depuración.
* **show_license** - muestra la licencia GPLv2.
* **help** - imprime esta ayuda.
