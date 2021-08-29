##
## Template file reference: digital-preservation-BR/data/in/RS/PortoAlegre/_pk027
## tplId: 027a
##
tplInputSchema_id=027a


## BASIC CONFIG
pg_io  =/tmp/pg_io
orig   =/var/www/preserv.addressforall.org/download
pg_uri =postgres://postgres@localhost
sandbox_root=/tmp/sandbox
need_commands= 7z v16+; psql v12+; shp2pgsql v3+; 
srid   =4686

pkid = 1
fullPkID=1_001
sandbox=$(sandbox_root)/_pk$(fullPkID)

## USER CONFIGS
pg_db  =ingest1
thisTplFile_root = digital-preservation-CO

## ## ## ## ## ## ##
## THIS_MAKE, _pk1

part1_file  =befe4d8cbbd51162e70f4f3dc4065acc430e20f2161073fabd007c575cd72098.zip
part1_name  =Malla Vial

part2_file  =fff3ae00d851d47c02d3b510d856526693a47250b4739b57cc6eaa88e0f57acd.zip
part2_name  =Lotes

part3_file  =ed072b0391d6c4a9bd76237b4ebb55de4f00ff0b73325d715d35baf29f41278e.zip
part3_name  =Quadras

part4_file  =8585490fefe89ff086a9234b27232cda9e29df9ad0b63d19acbd43f3760d04b5.zip
part4_name  =Ponto de Endereço

## COMPOSED VARS
pg_uri_db   =$(pg_uri)/$(pg_db)
part1_path  =$(orig)/$(part1_file)
part2_path  =$(orig)/$(part2_file)
part3_path  =$(orig)/$(part3_file)
part4_path  =$(orig)/$(part4_file)

all:
	@echo "=== Resumo deste makefile de recuperação de dados preservados ==="
	@printf "Targets para a geração de layers:\n\tall_layers via genericvia block geoaddress \n"
	@printf "Demais targets implementados:\n\tclean wget_files me\n"
	@echo "A gereação de layers requer os seguintes comandos e versões:\n\t$(need_commands)"

all_layers: via genericvia block geoaddress 
	@echo "--ALL LAYERS--"

## ## ## ## ## ## ## ## ##
## ## ## ## ## ## ## ## ##
## Make targets of the Project Digital Preservation

## ## ## ## sponsored by Project AddressForAll
geoaddress: layername = geoaddress_full
geoaddress: tabname = pk$(fullPkID)_p4_geoaddress
geoaddress: makedirs $(part4_path)
	@# pk1_p4 - ETL extrating to PostgreSQL/PostGIS the "geoaddress" datatype (point with house_number but no via name)
	@echo
	@echo "------------------------------------------"
	@echo "------ Layer tipo $(layername)  ------"
	@echo "-- Incluindo dados do arquivo-4 do package-$(fullPkID) na base $(pg_db) --"
	@echo " Nome-hash do arquivo-4: $(part4_file)"
	@echo " Tabela do layer geoaddress sem nome de rua, só com numero predial: $(tabname)"
	@echo " Sub-arquivos do arquivo-4 com o conteúdo alvo: pdom_shp/pdom.*"
	@echo " Tema dos sub-arquivos: $(part4_name)"
	@echo "Run with tmux and sudo! (DANGER: seems not idempotent on psql)"
	@whoami
	@printf "Above user is root? If not, you have permissions for all paths?\n [press ENTER for yes else ^C]"
	@read _press_enter_
	psql $(pg_uri_db) -c "DROP TABLE IF EXISTS $(tabname) CASCADE"
	@tput bold
	@echo Extracting ....
	@tput sgr0
	cd $(sandbox);  7z x -y  $(part4_path) "pdom_shp/pdom.*" ; chmod -R a+rx . > /dev/null
	@echo "Conferindo se SRID 4686 esta configurado:"
	@psql $(pg_uri_db) -c "SELECT srid, proj4text FROM spatial_ref_sys where srid=4686"
	@echo "Tudo bem até aqui?  [ENTER para continuar ou ^C para rodar WS/ingest-step1]"
	@read _tudo_bem_
ifeq ('shp2sql','shp2sql')
	@echo Executando shp2pgsql ...
	cd $(sandbox);	shp2pgsql   -s 4686 "pdom_shp/pdom.shp" $(tabname) | psql -q $(pg_uri_db) 2> /dev/null
endif
ifeq ('shp2sql','csv2sql')
	@echo Executando csc2sql ...
	psql $(pg_uri_db) -c "SELECT ingest.fdw_generate_direct_csv( '$(sandbox)/pdom_shp/pdom.csv', '$(tabname)' )"
endif
	psql $(pg_uri_db) -c "SELECT ingest.any_load('shp2sql','$(sandbox)/pdom_shp/pdom','$(layername)','$(tabname)','$(fullPkID)','$(part4_file)',array['gid', 'PDOTEXTO AS house_number', 'PDOCINTERI', 'PDONVIAL AS via_name', 'PDOTIPO', 'geom'])"
	@echo "Confira os resultados nas tabelas ingest.layer_file e ingest.feature_asis".
ifeq ('shp2sql','csv2sql')
	psql $(pg_uri_db) -c "DROP FOREIGN TABLE IF EXISTS $(tabname)"
endif
	@echo FIM.

geoaddress-clean: tabname = pk$(fullPkID)_p4_geoaddress
geoaddress-clean:
	rm -f "$(sandbox)/pdom_shp/pdom.*" || true
	psql $(pg_uri_db) -c "DROP TABLE IF EXISTS $(tabname) CASCADE"





## ## ## ## sponsored by Project AddressForAll
via: layername = via_full
via: tabname = pk$(fullPkID)_p1_via
via: makedirs $(part1_path)
	@# pk1_p1 - ETL extrating to PostgreSQL/PostGIS the "via" datatype (street axes)
	@echo
	@echo "------------------------------------------"
	@echo "------ Layer tipo $(layername)  ------"
	@echo "-- Incluindo dados do arquivo-1 do package-$(fullPkID) na base $(pg_db) --"
	@echo " Nome-hash do arquivo-1: $(part1_file)"
	@echo " Tabela do layer geoaddress sem nome de rua, só com numero predial: $(tabname)"
	@echo " Sub-arquivos do arquivo-1 com o conteúdo alvo: Malla_Vial_Integral_Bogota_D_C.*"
	@echo " Tema dos sub-arquivos: $(part1_name)"
	@echo "Run with tmux and sudo! (DANGER: seems not idempotent on psql)"
	@whoami
	@printf "Above user is root? If not, you have permissions for all paths?\n [press ENTER for yes else ^C]"
	@read _press_enter_
	psql $(pg_uri_db) -c "DROP TABLE IF EXISTS $(tabname) CASCADE"
	@tput bold
	@echo Extracting ....
	@tput sgr0
	cd $(sandbox);  7z x -y  $(part1_path) "Malla_Vial_Integral_Bogota_D_C.*"  ; chmod -R a+rx . > /dev/null
	@echo "Conferindo se SRID 4686 esta configurado:"
	@psql $(pg_uri_db) -c "SELECT srid, proj4text FROM spatial_ref_sys where srid=4686"
	@echo "Tudo bem até aqui?  [ENTER para continuar ou ^C para rodar WS/ingest-step1]"
	@read _tudo_bem_
ifeq ('shp2sql','shp2sql')
	@echo Executando shp2pgsql ...
	cd $(sandbox);	shp2pgsql   -s 4686 "Malla_Vial_Integral_Bogota_D_C.shp" $(tabname) | psql -q $(pg_uri_db) 2> /dev/null
endif
ifeq ('shp2sql','csv2sql')
	@echo Executando csc2sql ...
	psql $(pg_uri_db) -c "SELECT ingest.fdw_generate_direct_csv( '$(sandbox)/Malla_Vial_Integral_Bogota_D_C.csv', '$(tabname)' )"
endif
	psql $(pg_uri_db) -c "SELECT ingest.any_load('shp2sql','$(sandbox)/Malla_Vial_Integral_Bogota_D_C','$(layername)','$(tabname)','$(fullPkID)','$(part1_file)',array['gid', 'MVINOMBRE', 'MVINALTERN', 'MVINANTIGU', 'MVIETIQUET', 'geom'])"
	@echo "Confira os resultados nas tabelas ingest.layer_file e ingest.feature_asis".
ifeq ('shp2sql','csv2sql')
	psql $(pg_uri_db) -c "DROP FOREIGN TABLE IF EXISTS $(tabname)"
endif
	@echo FIM.

via-clean: tabname = pk$(fullPkID)_p1_via
via-clean:
	rm -f "$(sandbox)/Malla_Vial_Integral_Bogota_D_C.*" || true
	psql $(pg_uri_db) -c "DROP TABLE IF EXISTS $(tabname) CASCADE"




## ## ## ## sponsored by Project AddressForAll
block: layername = block_none
block: tabname = pk$(fullPkID)_p3_block
block: makedirs $(part3_path)
	@# pk1_p3 - ETL extrating to PostgreSQL/PostGIS the "block" datatype (street axes)
	@echo
	@echo "------------------------------------------"
	@echo "------ Layer tipo $(layername)  ------"
	@echo "-- Incluindo dados do arquivo-3 do package-$(fullPkID) na base $(pg_db) --"
	@echo " Nome-hash do arquivo-3: $(part3_file)"
	@echo " Tabela do layer geoaddress sem nome de rua, só com numero predial: $(tabname)"
	@echo " Sub-arquivos do arquivo-3 com o conteúdo alvo: manz_shp/manz.*"
	@echo " Tema dos sub-arquivos: $(part3_name)"
	@echo "Run with tmux and sudo! (DANGER: seems not idempotent on psql)"
	@whoami
	@printf "Above user is root? If not, you have permissions for all paths?\n [press ENTER for yes else ^C]"
	@read _press_enter_
	psql $(pg_uri_db) -c "DROP TABLE IF EXISTS $(tabname) CASCADE"
	@tput bold
	@echo Extracting ....
	@tput sgr0
	cd $(sandbox);  7z x -y  $(part3_path) "manz_shp/manz.*" ; chmod -R a+rx . > /dev/null
	@echo "Conferindo se SRID 4686 esta configurado:"
	@psql $(pg_uri_db) -c "SELECT srid, proj4text FROM spatial_ref_sys where srid=4686"
	@echo "Tudo bem até aqui?  [ENTER para continuar ou ^C para rodar WS/ingest-step1]"
	@read _tudo_bem_
ifeq ('shp2sql','shp2sql')
	@echo Executando shp2pgsql ...
	cd $(sandbox);	shp2pgsql   -s 4686 "manz_shp/manz.shp" $(tabname) | psql -q $(pg_uri_db) 2> /dev/null
endif
ifeq ('shp2sql','csv2sql')
	@echo Executando csc2sql ...
	psql $(pg_uri_db) -c "SELECT ingest.fdw_generate_direct_csv( '$(sandbox)/manz_shp/manz.csv', '$(tabname)' )"
endif
	psql $(pg_uri_db) -c "SELECT ingest.any_load('shp2sql','$(sandbox)/manz_shp/manz','$(layername)','$(tabname)','$(fullPkID)','$(part3_file)',array['gid', 'geom'])"
	@echo "Confira os resultados nas tabelas ingest.layer_file e ingest.feature_asis".
ifeq ('shp2sql','csv2sql')
	psql $(pg_uri_db) -c "DROP FOREIGN TABLE IF EXISTS $(tabname)"
endif
	@echo FIM.

block-clean: tabname = pk$(fullPkID)_p3_block
block-clean:
	rm -f "$(sandbox)/manz_shp/manz.*" || true
	psql $(pg_uri_db) -c "DROP TABLE IF EXISTS $(tabname) CASCADE"



## ## ## ## sponsored by Project AddressForAll
genericvia: layername = genericvia_none
genericvia: tabname = pk$(fullPkID)_p2_genericvia
genericvia: makedirs $(part2_path)
	@# pk1_p2 - ETL extrating to PostgreSQL/PostGIS the "genericvia" datatype (railroad, waterway or other)
	@echo
	@echo "------------------------------------------"
	@echo "------ Layer tipo $(layername)  ------"
	@echo "-- Incluindo dados do arquivo-2 do package-$(fullPkID) na base $(pg_db) --"
	@echo " Nome-hash do arquivo-2: $(part2_file)"
	@echo " Tabela do layer geoaddress sem nome de rua, só com numero predial: $(tabname)"
	@echo " Sub-arquivos do arquivo-2 com o conteúdo alvo: lote_shp/lote.*"
	@echo " Tema dos sub-arquivos: $(part2_name)"
	@echo "Run with tmux and sudo! (DANGER: seems not idempotent on psql)"
	@whoami
	@printf "Above user is root? If not, you have permissions for all paths?\n [press ENTER for yes else ^C]"
	@read _press_enter_
	psql $(pg_uri_db) -c "DROP TABLE IF EXISTS $(tabname) CASCADE"
	@tput bold
	@echo Extracting ....
	@tput sgr0
	cd $(sandbox);  7z x -y  $(part2_path) "lote_shp/lote.*" ; chmod -R a+rx . > /dev/null
	@echo "Conferindo se SRID 4686 esta configurado:"
	@psql $(pg_uri_db) -c "SELECT srid, proj4text FROM spatial_ref_sys where srid=4686"
	@echo "Tudo bem até aqui?  [ENTER para continuar ou ^C para rodar WS/ingest-step1]"
	@read _tudo_bem_
ifeq ('shp2sql','shp2sql')
	@echo Executando shp2pgsql ...
	cd $(sandbox);	shp2pgsql   -s 4686 "lote_shp/lote.shp" $(tabname) | psql -q $(pg_uri_db) 2> /dev/null
endif
ifeq ('shp2sql','csv2sql')
	@echo Executando csc2sql ...
	psql $(pg_uri_db) -c "SELECT ingest.fdw_generate_direct_csv( '$(sandbox)/lote_shp/lote.csv', '$(tabname)' )"
endif
	psql $(pg_uri_db) -c "SELECT ingest.any_load('shp2sql','$(sandbox)/lote_shp/lote','$(layername)','$(tabname)','$(fullPkID)','$(part2_file)',array['gid', 'geom'])"
	@echo "Confira os resultados nas tabelas ingest.layer_file e ingest.feature_asis".
ifeq ('shp2sql','csv2sql')
	psql $(pg_uri_db) -c "DROP FOREIGN TABLE IF EXISTS $(tabname)"
endif
	@echo FIM.

genericvia-clean: tabname = pk$(fullPkID)_p2_genericvia
genericvia-clean:
	rm -f "$(sandbox)/lote_shp/lote.*" || true
	psql $(pg_uri_db) -c "DROP TABLE IF EXISTS $(tabname) CASCADE"




## ## ## ## ## ## ## ## ##
## ## ## ## ## ## ## ## ##

makedirs: clean_sandbox
	@mkdir -p $(sandbox_root)
	@mkdir -p $(sandbox)
	@mkdir -p $(pg_io)

wget_files:
	@echo "Under construction, need to check that orig path is not /var/www! or use orig=x [ENTER if not else ^C]"
	@echo $(orig)
	@read _ENTER_OK_
	mkdir -p $(orig)
	@cd $(orig); wget http://preserv.addressforall.org/download/befe4d8cbbd51162e70f4f3dc4065acc430e20f2161073fabd007c575cd72098.zip
	@cd $(orig); wget http://preserv.addressforall.org/download/fff3ae00d851d47c02d3b510d856526693a47250b4739b57cc6eaa88e0f57acd.zip
	@cd $(orig); wget http://preserv.addressforall.org/download/ed072b0391d6c4a9bd76237b4ebb55de4f00ff0b73325d715d35baf29f41278e.zip
	@cd $(orig); wget http://preserv.addressforall.org/download/8585490fefe89ff086a9234b27232cda9e29df9ad0b63d19acbd43f3760d04b5.zip
	@echo "Please, if orig not default, run 'make _target_ orig=$(orig)'"


## ## ## ##

clean_sandbox:
	@rm -rf $(sandbox) || true

clean: geoaddress-clean nsvia-clean via-clean


## ############################
## SELF-GENERATE MAKE (make me)
##

thisTplFile  = digital-preservation-CO/src/maketemplates/make_ref027a.mustache.mk
country      = CO
baseSrc      = /opt/gits/_a4a

srcPy        =  $(baseSrc)/digital-preservation/src/run_mustache.py
mkme_input0  =  $(baseSrc)/digital-preservation-$(country)/src/maketemplates/commomFirst.yaml
mkme_srcTplLast  =  $(baseSrc)/digital-preservation-$(country)/src/maketemplates/commomLast.mustache.mk
mkme_srcTpl  =  $(baseSrc)/$(thisTplFile)
mkme_input   = ./make_conf.yaml
mkme_output  = /tmp/digitalPresservation-make_me.mk


me: $(srcPy) $(mkme_input0) $(mkme_input) $(mkme_srcTpl)
	@echo "-- Updating this make --"
	python3 $(srcPy) -b $(baseSrc)/ -t $(mkme_srcTpl) --tplLast=$(mkme_srcTplLast) -i $(mkme_input) --input0=$(mkme_input0) > $(mkme_output)
	@echo " Check diff, the '<' lines are the new ones... Something changed?"
	@diff $(mkme_output) ./makefile || :
	@echo "If some changes, and no error in the changes, move the script:"
	@echo " mv ./makefile ./bk-makefile"
	@echo " mv $(mkme_output) ./makefile"
	@echo "[ENTER para rodar mv ou ^C para sair]"
	@read _tudo_bem_
	mv $(mkme_output) ./makefile

