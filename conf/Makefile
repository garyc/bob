SHELL := /bin/bash

exec_report =       \
	mkdir -p $1;      \
	set -eo pipefail; \
	$2 2>&1 | tee $3; \

exec_sum =                              \
	$1sum $2 | awk '{print $$1}' > $2.$1; \

init:
	cd ${app}

dep: init
	npm install .

rmdep: init
	rm -rf node_modules

updep:
	bob _updep
	
tools:
	npm install -g dox@0.x esprima@1.x expresso@0.x jscheckstyle@0.x jshint@0.x mocha@1.x nodelint@0.x nodeunit@0.x vows@0.x

rmtools:
	npm rm -g dox@0.x esprima@1.x expresso@0.x jscheckstyle@0.x jshint@0.x mocha@1.x nodelint@0.x vows@0.x

clean: init
	rm -rf .bob nohup.* *.log

lint: init
	$(call exec_report,.bob/report,${lint_bin} ${lint_opts} ${lint_files},.bob/report/lint.out)

style: init
	$(call exec_report,.bob/report,${style_bin} ${style_opts} ${style_files},.bob/report/style.out)

test: init
	$(call exec_report,.bob/report,${test_bin} ${test_opts} ${test_files},.bob/report/test.out)

test-integration: init
	$(call exec_report,.bob/report,${test-integration_bin} ${test-integration_opts} ${test-integration_files},.bob/report/test-integration.out)

test-acceptance: init
	$(call exec_report,.bob/report,${test-acceptance_bin} ${test-acceptance_opts} ${test-acceptance_files},.bob/report/test-acceptance.out)

test-npm:
	npm test

instrument-jscoverage: init
	rm -rf .bob/instrumented/
	mkdir -p .bob/instrumented/
	${bob}/node_modules/expresso/deps/jscoverage/node-jscoverage lib .bob/instrumented/lib
	cp -r test .bob/instrumented
	ln -s ${app}/package.json .bob/instrumented/package.json
	ln -s ${app}/node_modules .bob/instrumented/node_modules

coverage: ${coverage_dep}
	$(call exec_report,.bob/report,${coverage_bin} ${coverage_opts} ${coverage_files},.bob/report/coverage.out)

doc: init
	$(call exec_report,.bob/report,${doc_bin} ${doc_opts} ${doc_files},.bob/report/doc.out)

depgraph: init
	$(call exec_report,.bob/report,${depgraph_bin} ${depgraph_opts} ${depgraph_files},.bob/report/depgraph.out)
	echo "Created module dependencies graph: .bob/report/depgraph.png"

package: init
	mkdir -p .bob/artifact/${name}-${version}
	rsync -av --exclude-from=${bob}/conf/package-exclude ${src}/ .bob/artifact/${name}-${version}
	cd .bob/artifact/; tar -X ${bob}/conf/package-exclude -cvzf ${name}-${version}.tar.gz ${name}-${version}/
	$(call exec_sum,md5,.bob/artifact/${name}-${version}.tar.gz)
	$(call exec_sum,sha1,.bob/artifact/${name}-${version}.tar.gz)

publish: ${publish_dep}

publish-ivy: init
	mkdir -p .bob/artifact/
	cp ivy.xml .bob/artifact/ivy.xml
	bob _template
	$(call exec_sum,md5,.bob/artifact/ivy.xml)
	$(call exec_sum,sha1,.bob/artifact/ivy.xml)
	rm -rf .bob/artifact/${name}-${version}
	ssh -i ${publish_key} -p ${publish_port} ${publish_user}@${publish_host} 'mkdir -p ${publish_dir}'
	scp -i ${publish_key} -P ${publish_port} -r .bob/artifact/* ${publish_user}@${publish_host}:${publish_dir}

publish-npm: init
	npm publish

send: init
	$(call exec_report,.bob/report,${send_bin} ${send_opts} ${send_files},.bob/report/send.out)

stop:
	NODE_ENV=${NODE_ENV} node ${name}.js stop
  
stop-npm:
	npm stop

start:
	NODE_ENV=${NODE_ENV} node ${name}.js start
  
start-npm:
	npm start

restart:
	NODE_ENV=${NODE_ENV} node ${name}.js restart

restart-npm:
	npm restart

status:
	NODE_ENV=${NODE_ENV} node ${name}.js status

versionup:
	bob _versionup

versionup-minor:
	bob _versionup-minor

versionup-major:
	bob _versionup-major

nuke:
	kill -9 `ps -ef | sed -n '/node'""'/{/grep/!p;}' | awk '{print $$2}'`
