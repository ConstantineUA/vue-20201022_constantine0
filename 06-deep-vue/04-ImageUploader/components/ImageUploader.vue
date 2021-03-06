<template>
  <div class="image-uploader">
    <label
      class="image-uploader__preview"
      :class="{'image-uploader__preview-loading': this.loading}"
      :style="imageStyles"
      @click="resetImage"
    >
      <span>{{ previewText }}</span>
      <input
        type="file"
        accept="image/*"
        class="form-control-file"
        @change="uploadImage"
        :disabled="loading"
        :value="imageId"
      />
    </label>
  </div>
</template>

<script>
import { ImageService } from '../image-service';

export default {
  name: 'ImageUploader',

  data() {
    return {
      loading: false,
    };
  },

  props: {
    imageId: {
      type: Number,
      default: null,
    },
  },

  model: {
    prop: 'imageId',
    event: 'change',
  },

  computed: {
    previewText() {
      if (this.loading) {
        return 'Загрузка...';
      }

      if (this.imageId !== null) {
        return 'Удалить изображение';
      }

      return 'Загрузить изображение';
    },

    imageStyles() {
      if (this.imageId) {
        return {
          '--bg-image': `url('${ImageService.getImageURL(this.imageId)}')`,
        };
      }

      return {};
    },
  },

  methods: {
    uploadImage(event) {
      this.loading = true;

      ImageService.uploadImage(event.target.files[0]).then(imageData => {
        this.loading = false;
        this.$emit('change', imageData.id);
      });
    },

    resetImage(event) {
      if (this.imageId) {
        this.$emit('change', null);

        event.preventDefault();
      }
    },
  },
};
</script>

<style scoped>
.image-uploader .form-control-file {
  opacity: 0;
  height: 0;
}

.image-uploader .image-uploader__preview {
  --bg-image: var(--default-cover);
  background-size: cover;
  background-position: center;
  background-image: linear-gradient(
      0deg,
      rgba(0, 0, 0, 0.4),
      rgba(0, 0, 0, 0.4)
    ),
    var(--bg-image);
  border: 2px solid var(--blue-light);
  border-radius: 8px;
  transition: 0.2s border-color;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  max-width: 512px;
  height: 228px;
}

.image-uploader .image-uploader__preview > span {
  color: var(--white);
  font-family: 'Nunito', sans-serif;
  font-weight: 600;
  font-size: 20px;
  line-height: 28px;
}

.image-uploader .image-uploader__preview:hover {
  border-color: var(--blue);
}

.image-uploader .image-uploader__preview.image-uploader__preview-loading {
  cursor: no-drop;
}
</style>
