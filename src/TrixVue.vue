<template>
  <div class="trixWrapper">
    <div ref="trixContainer" :id="id">
      <trix-editor input="x" @trix-change="handleTyping" @trix-attachment-add="handleUpload"></trix-editor>
      <input type="hidden" :value="content" id="x">
    </div>
  </div>
</template>

<script>
  import '../../../static/trix/trix'
  import '../../../static/trix/trix.css'

  export default {
    name: 'trix-vue',
    props: {
      id: {
        type: String,
        default: 'trix-container'
      },
      content: {
        type: String,
        default: ''
      }
    },
    data() {
      return {
        host: "https://d13txem1unpe48.cloudfront.net/"
      }
    },
    methods: {
      handleTyping(event) {
        const element = event.target
        const html = element.value
        this.$emit('update:content', html)
      },
      handleUpload(event) {
        console.log(event)
      },
      uploadAttachment(attachment) {
        var file, form, key, xhr;
        file = attachment.file;
        key = this.createStorageKey(file);
        form = new FormData;
        form.append("key", key);
        form.append("Content-Type", file.type);
        form.append("file", file);
        xhr = new XMLHttpRequest;
        xhr.open("POST", this.host, true);
        xhr.upload.onprogress = function (event) {
          var progress;
          progress = event.loaded / event.total * 100;
          return attachment.setUploadProgress(progress);
        };
        xhr.onload = function () {
          var href, url;
          if (xhr.status === 204) {
            url = href = this.host + key;
            return attachment.setAttributes({
              url: url,
              href: href
            });
          }
        };
        return xhr.send(form);
      },
      createStorageKey(file) {
        var date, day, time;
        date = new Date();
        day = date.toISOString().slice(0, 10);
        time = date.getTime();
        return "tmp/" + day + "/" + time + "-" + file.name;
      }
    },
    mounted() {
      // document.addEventListener("trix-attachment-add", function (event) {
      //   var attachment
      //   attachment = event.attachment
      //   if (attachment.file) {
      //     return this.uploadAttachment(attachment)
      //   }
      // }.bind(this))

    }
  }

</script>