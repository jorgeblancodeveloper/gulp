# gulp
Mis rutinas Gulp
compilar sass:

    var sass = require('gulp-sass');
    gulp.task('sass', function(){
    gulp.src('private-zone-design/source_scss/*.scss')
    .pipe(sourcemaps.init())
    .pipe(sass({outputStyle: 'compressed'}).on('error', sass.logError))
    .pipe(sourcemaps.write("../../private-zone-design/source_scss/maps"))
    .pipe(gulp.dest('private-zone-design/compiled_css/'));
    });

Pasar todo a minusculas:

    var rename = require('gulp-rename');
    gulp.task("lower", function () {
    return gulp.src('private-zone-design/sites/scss_fuentes/*.scss')
    .pipe(rename(function(path) {
    path.dirname = changeCase.lowerCase(path.dirname);
    path.basename = changeCase.lowerCase(path.basename);
    path.extname = changeCase.lowerCase(path.extname);
    })) 
    .pipe(gulp.dest('private-zone-design/sites/scss_fuentes/*.scss'));
    });

Añadir prefijos vendor:

    const autoprefixer = require('gulp-autoprefixer');
    gulp.task('prefija', function(){
    gulp.src('private-zone-design/css_server/*.css')
    .pipe(autoprefixer({
    browsers: ['last 4 versions']
    }))
    .pipe(gulp.dest('private-zone-design/css_prefixed/'))
    });

Borrar carpetas/archivos

    var del = require('del');
    gulp.task('clean', function () {
    return del([
    'private-zone-design/sites/**/*.{css,map}'
    ]);
    });


Concatena los archivos indicados en el json de los sitios del array:

    var gulp = require('gulp');
    var concat = require('gulp-concat');
    var uglify = require('gulp-uglify');
    var jshint = require('gulp-jshint');
    var sites = ["site1.com", "site2.com", "site3.mobi", "site4.mobi"];
    gulp.task('compilejs', function() {
    var i, j;
    var taskName;
    for (i = 0; i < sites.length; i++) {
       var mimod = JSON.parse(fs.readFileSync('sites/' + sites[i] + '/js/config.json'));
       for (j = 0; j < mimod.modules.length; j++) {
           mimod.modules[j] = "modules_js/" + mimod.modules[j];
           console.log("modulo " + mimod.modules[j]);
           gulp.src(mimod.modules)
               .pipe(jshint())
               .pipe(jshint.reporter());
               .pipe(concat('main.js'))
               .pipe(uglify())
               .pipe(gulp.dest('sites/' + sites[i] + '/js'));
       }
    }
    });

Verificar que existen los archivos indicados en la configuración:

    var gulp = require('gulp');  
    var contains = require('gulp-contains');
    var fs = require('fs'); 
    var counter = 0; 
    gulp.task('check', function() {  
       gulp.src('es/**/trivia.txt')   
       .pipe(contains({  
               search: 'logo_lang',
               onFound: function(string, file, cb) {
                   try {
                       fs.accessSync(file.path.slice(0, -10) + "img/logo_lang.png");
                   } catch (e) { 
                       counter ++;
                       console.log(counter + " aqui falta: " + file.path.slice(0, -10));
                       return false; 
                   }
                   return false;
               }
           }))
        .pipe(gulp.dest(./))
    });

Guardar capturas de las urls de un json:

    const Pageres = require('pageres');
    var config = require('./config_screen.json');
    var index=0;
    gulp.task("capturas", function () {
    for (var i = 0; i < Object.keys(config.links).length; i++) {
    for (var j = 0; j < config.links[Object.keys(config.links)[i]].length; j++) {
      index++;
      console.log(index+" "+config.links[Object.keys(config.links)[i]][j]);
      new Pageres({delay: 5})
      .src(config.links[Object.keys(config.links)[i]][j], ['360x640'], {crop: true})
      .dest("screenshots/"+Object.keys(config.links)[i])
      .run()
    }
    }
    });

El json debe ser asi:

      {
          "links": {
              "web1": [
                  "link", 
                  "link"],
              "web2": [
                  "link", 
                  "link"]
          }
      }

Chequear CSS:

      var csscss = require('gulp-csscss');
      gulp.task('revisa_css', function() {
      gulp.src('private-zone-design/compiled_css/*.css')
      .pipe(csscss())
      });

DESTINOS:

Guardar en directorio replicando estructura:

        .pipe(gulp.dest("adwebs-design/compiled_css"));
 
Guardar en directorio sin estructura:

    .pipe(gulp.dest(function(file) {
    file.path = file.base + path.basename(file.path);
    return 'adwebs-design/compiled_css';
    }));

Guardar en mismo directorio archivo origen:

    .pipe(gulp.dest(function (file) {
    return file.base;
    }));
