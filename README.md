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
var sites = ["ES_etrainingfit.com", "ES_recetas-tv.com", "ES_todo-viral.mobi", "IT_gameflix.mobi"];
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
                   fs.accessSync(file.path.slice(0, -10) + "img/geo.png");
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
        "Horo8-1": [
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=tarot&rwpid=01&rwtrack=2018015a58b5fe2509eb57568b4ad1", 
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=tarot&rwpid=02&rwtrack=2018015a58b616a7e479482b8b4acd"],
        "Game club 4-2": [
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=28&rwtrack=2018015a58b46be67632f7018b4ade", 
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=29&rwtrack=2018015a58b4c72509eb6b568b4aa2", 
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=30&rwtrack=2018015a58b4de2509eb56568b4abb", 
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=31&rwtrack=2018015a58b4f22509eb66568b4aad", 
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=32&rwtrack=2018015a58b4fe2509eb53568b4acf"],
        
        "Game club 4-1": [
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=01&rwtrack=2018015a58b10c649ba3fb258b4a72",
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=09&rwtrack=2018015a58b18c2509eb52568b4ac5",
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=10&rwtrack=2018015a58b19f2509eb54568b4ab9",
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=22&rwtrack=2018015a58b1af649ba3ea258b4ab3",
            "http://fun-cell.mobi/?country=th&rwidt=240&rwcat=game&rwpid=25&rwtrack=2018015a58b1bf649ba3df258b4aea"
        ]
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

Guardar en directorio aplanando estructura:
.pipe(gulp.dest(function(file) {
file.path = file.base + path.basename(file.path);
return 'adwebs-design/compiled_css';
}));

Guardar en mismo directorio archivo origen:
.pipe(gulp.dest(function (file) {
return file.base;
}));
