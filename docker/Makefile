minio-up:
	docker-compose up -d minio

up:
	docker-compose up registry

down:
	docker-compose down

minio-set-alias:
	mc alias set minio http://localhost:9000/ an_user a_password

create-bucket:
	mc mb minio/registry-bucket

vagrant-up:
	vagrant up --provider=docker