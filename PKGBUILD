# Maintainer: Mihail Strashun <m.strashun@gmail.com> aka Volfram
pkgname=dmd2-complete
pkgver=2.057
pkgrel=1
pkgdesc="The Digital Mars D compiler & Standard Library (D2 version)"
arch=('i686' 'x86_64')
url="http://www.digitalmars.com/d/2.0/"
source=(http://ftp.digitalmars.com/dmd.$pkgver.zip)
md5sums=('531c4b60eb002ea8abbe5c80b2eb677d')
provides=('d-compiler='$pkgver 'dmd2='$pkgver 'libphobos2='$pkgver)
license=('custom')
options=('!strip' 'docs')
conflicts=('libtango' 'dmd2' 'dmd2-bin')
depends=('gcc-libs')

if [ $CARCH = 'x86_64' ]
then
	archstr="64"
fi

if [ $CARCH = 'i686' ]
then 
	archstr="32"
fi

build() {
   # Build and install dmd binary
   cd $srcdir/dmd2/src/dmd 
   make -f posix.mak MODEL=$archstr
   install -Dm755 ./dmd $pkgdir/usr/bin/dmd 

   oldpath=$PATH
   export PATH=$PATH:`pwd`

   # Copy additional tools
   install -Dm755 $srcdir/dmd2/linux/bin$archstr/dumpobj $pkgdir/usr/bin/dumpobj 
   install -Dm755 $srcdir/dmd2/linux/bin$archstr/obj2asm $pkgdir/usr/bin/obj2asm 
   install -Dm755 $srcdir/dmd2/linux/bin$archstr/rdmd $pkgdir/usr/bin/rdmd 
   echo -e "[Environment]\nDFLAGS=-m$archstr -I/usr/include/d -I/usr/include/d/druntime/import -L-L/usr/lib -L-lrt" > $startdir/dmd.conf
   install -Dm644 $startdir/dmd.conf $pkgdir/etc/dmd.conf 

   # Copy the license
   install -Dm644 $srcdir/dmd2/license.txt $pkgdir/usr/share/licenses/dmd/COPYING 

   # Copy man files   
   for x in $srcdir/dmd2/man/man1/*.1; do
       install -Dm644 $x "$pkgdir/usr/share/man/man1/$(basename $x)"
   done

   for x in $srcdir/dmd2/man/man1/*.5; do
       install -Dm644 $x "$pkgdir/usr/share/man/man5/$(basename $x)"
   done

   # Copy documentation
   mkdir -p $pkgdir/usr/share/doc/d/phobos
   docs="$srcdir/dmd2/html/d"
   for x in $(find $docs/*.html $docs/*.gif $docs/*.ico $docs/*.jpg $docs/*.css); do
       install -Dm644 $x "$pkgdir/usr/share/doc/d/$(basename $x)"
   done

   for x in $(find $docs/phobos/*.html $docs/phobos/*.gif $docs/phobos/*.css); do
       install -Dm644 $x "$pkgdir/usr/share/doc/d/phobos/$(basename $x)"
   done

   # Standard library and runtime

# Build and install druntime
	cd $srcdir/dmd2/src/druntime 
	make -f posix.mak MODEL=$archstr 
	install -Dm644 ./lib/libdruntime.a $pkgdir/usr/lib/libdruntime.a 

# Build and install standard library binary
	cd $srcdir/dmd2/src/phobos 
	make -f posix.mak MODEL=$archstr
	install -Dm644 ./generated/linux/release/$archstr/libphobos2.a $pkgdir/usr/lib/libphobos2.a 

# Install standard library *.d modules
	mkdir -p $pkgdir/usr/include/d
	cd $srcdir/dmd2/src/phobos
	cp -Rf std $pkgdir/usr/include/d 
	cp -Rf etc $pkgdir/usr/include/d 
	cp -f {crc32,index,unittest}.d $pkgdir/usr/include/d 

# Install druntime *.d modules
	mkdir -p $pkgdir/usr/include/d/druntime
	cd $srcdir/dmd2/src/druntime/
	cp -Rf import $pkgdir/usr/include/d/druntime 

# Copy license
	install -Dm644 $srcdir/dmd2/src/phobos/phoboslicense.txt $pkgdir/usr/share/licenses/$pkgname/LICENSE 

	export PATH=$oldpath
}
